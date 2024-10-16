## btrfs 子卷（Subvolumes）详解

**btrfs** 是一个现代的、功能强大的 Linux 文件系统，支持高级功能如子卷（subvolumes）、快照（snapshots）、压缩、RAID 等。子卷是 btrfs 的核心概念之一，允许用户在单一文件系统内创建多个独立的、可管理的文件系统实例。本文将深入探讨 btrfs 子卷的概念、创建与管理方法，并结合你在 Arch Linux 上的配置，展示如何有效利用子卷优化系统结构。

### 目录

1. [什么是 btrfs 子卷](#1-什么是-btrfs-子卷)
2. [子卷的优势](#2-子卷的优势)
3. [创建和管理子卷](#3-创建和管理子卷)
4. [在 Arch Linux 上使用子卷](#4-在-arch-linux-上使用子卷)
5. [配置 `/etc/fstab`](#5-配置-etcfstab)
6. [使用子卷进行快照管理](#6-使用子卷进行快照管理)
7. [备份与恢复子卷](#7-备份与恢复子卷)
8. [最佳实践与注意事项](#8-最佳实践与注意事项)
9. [总结](#9-总结)

---

## 1. 什么是 btrfs 子卷

**子卷（Subvolume）** 是 btrfs 文件系统内的独立文件系统实例，具有自己的命名空间和挂载点。它们共享同一个物理存储设备，但在逻辑上相互独立。子卷允许用户更细粒度地管理文件系统结构、权限和快照。

### 关键特性

- **独立挂载**：每个子卷可以单独挂载到不同的挂载点。
- **快照支持**：可以轻松创建和管理子卷的快照，用于备份和恢复。
- **权限与隔离**：不同子卷之间可以有不同的权限和配置，增强系统安全性。
- **灵活性**：允许在同一个文件系统中组织不同类型的数据，提高存储利用率。

## 2. 子卷的优势

- **组织与管理**：通过子卷，可以将不同的目录结构分离，便于管理。例如，将 `/`、`/home` 和 `/var` 分别放在不同的子卷中。
- **快照与备份**：子卷支持快速创建一致性的快照，适用于系统恢复、版本控制和备份。
- **灵活的挂载选项**：不同子卷可以有不同的挂载选项，如压缩、空间缓存等，提高性能和存储效率。
- **隔离与安全**：将关键系统目录与用户数据分离，增强系统的安全性和稳定性。

## 3. 创建和管理子卷

### 3.1 基本概念

- **根子卷**：默认的顶层子卷，通常为 `/`（根文件系统）。
- **独立子卷**：在根子卷下创建的其他子卷，如 `@home`、`@var` 等。

### 3.2 创建子卷

假设你已经在 SSD 上创建了一个 btrfs 根分区，并将其挂载到 `/mnt`。现在，你想创建用于 `/`（根）、`/efi` 和 `/home` 的子卷。

#### 步骤

1. **挂载根分区**

   ```bash
   mount /dev/sda2 /mnt
   ```

2. **创建子卷**

   ```bash
   btrfs subvolume create /mnt/@
   btrfs subvolume create /mnt/@home
   btrfs subvolume create /mnt/@efi
   ```

   **解释**：
   - `/mnt/@`：用于挂载根文件系统 `/`。
   - `/mnt/@home`：用于挂载用户主目录 `/home`。
   - `/mnt/@efi`：用于挂载 EFI 分区 `/efi`。

3. **卸载并重新挂载子卷**

   ```bash
   umount /mnt
   ```

   然后，按照下面的步骤在安装过程中正确挂载子卷。

### 3.3 删除子卷

**警告**：删除子卷将永久丢失其中的数据，操作前请确保备份重要数据。

```bash
btrfs subvolume delete /path/to/subvolume
```

### 3.4 查看子卷

列出所有子卷：

```bash
btrfs subvolume list /mnt
```

## 4. 在 Arch Linux 上使用子卷

结合你之前的配置（SSD 上的 `/` 和 `/efi`，HDD 上的 `/home`），以下是详细的步骤，展示如何在 Arch Linux 安装过程中使用子卷。

### 4.1 分区与格式化

假设：
- **SSD**：`/dev/sda`
  - `/dev/sda1`：EFI 分区
  - `/dev/sda2`：btrfs 根分区
- **HDD**：`/dev/sdb1`：btrfs `/home` 分区

#### 格式化分区

```bash
mkfs.fat -F32 /dev/sda1
mkfs.btrfs -f /dev/sda2
mkfs.btrfs -f /dev/sdb1
```

### 4.2 创建并挂载子卷

1. **挂载 SSD 根分区**

   ```bash
   mount /dev/sda2 /mnt
   ```

2. **创建子卷**

   ```bash
   btrfs subvolume create /mnt/@
   btrfs subvolume create /mnt/@efi
   ```

3. **挂载子卷**

   - **卸载根分区**

     ```bash
     umount /mnt
     ```

   - **挂载根子卷**

     ```bash
     mount -o subvol=@,compress=zstd,space_cache /dev/sda2 /mnt
     ```

   - **创建必要目录**

     ```bash
     mkdir -p /mnt/efi
     mkdir -p /mnt/home
     ```

   - **挂载 EFI 子卷**

     ```bash
     mount -o subvol=@efi,compress=zstd,space_cache /dev/sda2 /mnt/efi
     ```

   - **挂载 `/home` 分区**

     ```bash
     mount /dev/sdb1 /mnt/home
     ```

   **注意**：如果你希望 `/home` 也是一个子卷，需在 `/mnt/home` 上创建子卷。

4. **在 `/home` 分区创建子卷**

   ```bash
   btrfs subvolume create /mnt/home/@
   ```

5. **重新挂载 `/home` 子卷**

   ```bash
   umount /mnt/home
   mount -o subvol=@,compress=zstd,space_cache /dev/sdb1 /mnt/home
   ```

### 4.3 安装基本系统

继续按照之前的步骤安装基本系统：

```bash
pacstrap /mnt base linux linux-firmware btrfs-progs
```

### 4.4 生成 `fstab`

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

## 5. 配置 `/etc/fstab`

为了确保子卷在启动时正确挂载，需要手动编辑 `/etc/fstab`。

### 示例 `fstab` 配置

假设：
- **根子卷**：`@`
- **EFI 子卷**：`@efi`
- **`/home` 子卷**：`@home`
- **UUID 获取**

首先，获取各分区的 UUID：

```bash
blkid
```

假设输出如下：

```
/dev/sda1: UUID="XXXX-XXXX" TYPE="vfat"
/dev/sda2: UUID="YYYY-YYYY" TYPE="btrfs"
/dev/sdb1: UUID="ZZZZ-ZZZZ" TYPE="btrfs"
```

### 编辑 `/etc/fstab`

使用编辑器打开 `/etc/fstab`：

```bash
nano /etc/fstab
```

添加以下条目：

```fstab
# 根文件系统
UUID=YYYY-YYYY / btrfs defaults,subvol=@,compress=zstd,space_cache 0 0

# EFI 分区
UUID=XXXX-XXXX /efi vfat defaults,noatime 0 0

# /home 文件系统
UUID=ZZZZ-ZZZZ /home btrfs defaults,subvol=@home,compress=zstd,space_cache 0 0
```

**选项解释**：

- `defaults`：默认挂载选项。
- `subvol=@`：指定挂载子卷 `@` 作为根文件系统。
- `compress=zstd`：启用 Zstandard 压缩，提高存储效率。
- `space_cache`：启用空间缓存，加快文件系统操作。
- `noatime`（仅用于 EFI 分区）：禁用访问时间记录，提升性能。

### 完整示例 `fstab`

```fstab
# /etc/fstab: static file system information.
#
# <file system> <mount point> <type> <options> <dump> <pass>
UUID=YYYY-YYYY / btrfs defaults,subvol=@,compress=zstd,space_cache 0 0
UUID=XXXX-XXXX /efi vfat defaults,noatime 0 0
UUID=ZZZZ-ZZZZ /home btrfs defaults,subvol=@home,compress=zstd,space_cache 0 0
```

## 6. 使用子卷进行快照管理

快照是子卷的只读或可写副本，适用于备份、恢复和版本控制。以下是如何创建和管理快照的步骤。

### 6.1 创建快照

#### 6.1.1 创建根子卷的快照

```bash
btrfs subvolume snapshot /mnt/@ /mnt/@snapshots/root_$(date +%Y%m%d)
```

#### 6.1.2 创建 `/home` 子卷的快照

```bash
btrfs subvolume snapshot /mnt/home/@ /mnt/home/@snapshots/home_$(date +%Y%m%d)
```

**解释**：

- `/mnt/@snapshots/`：快照存放目录，需要提前创建。

### 6.2 自动化快照

可以使用脚本或工具（如 **snapper**、**btrbk**）自动化快照创建和管理。

#### 示例：使用 `snapper`

1. **安装 snapper**

   ```bash
   pacman -S snapper
   ```

2. **创建 snapper 配置**

   ```bash
   snapper -c root create-config /
   snapper -c home create-config /home
   ```

3. **配置 snapper**

   编辑 `/etc/snapper/configs/root` 和 `/etc/snapper/configs/home`，根据需求调整配置参数。

4. **启用定时任务**

   ```bash
   systemctl enable snapper-timeline.timer
   systemctl start snapper-timeline.timer
   systemctl enable snapper-cleanup.timer
   systemctl start snapper-cleanup.timer
   ```

### 6.3 恢复快照

#### 6.3.1 恢复根子卷快照

**警告**：恢复快照会覆盖当前子卷数据，操作前请备份重要数据。

1. **删除当前子卷**

   ```bash
   btrfs subvolume delete /mnt/@
   ```

2. **恢复快照**

   ```bash
   btrfs subvolume snapshot /mnt/@snapshots/root_20240101 /mnt/@
   ```

3. **重新挂载子卷**

   ```bash
   umount /mnt
   mount -o subvol=@,compress=zstd,space_cache /dev/sda2 /mnt
   ```

#### 6.3.2 恢复 `/home` 子卷快照

类似于根子卷的恢复过程。

## 7. 备份与恢复子卷

尽管 btrfs 提供了快照功能，但定期备份仍然至关重要，特别是在进行系统更新或重大更改之前。

### 7.1 使用 `btrfs send` 和 `btrfs receive`

这对命令允许你将子卷的快照以增量的方式发送到备份存储设备。

#### 步骤

1. **创建快照**

   ```bash
   btrfs subvolume snapshot /mnt/@ /mnt/@snapshots/root_$(date +%Y%m%d)
   ```

2. **发送快照到备份位置**

   ```bash
   btrfs send /mnt/@snapshots/root_20240101 | ssh user@backup-server "btrfs receive /path/to/backup/"
   ```

   **解释**：
   - `btrfs send`：生成快照数据流。
   - `ssh user@backup-server "btrfs receive /path/to/backup/"`：通过 SSH 将快照接收并存储到备份服务器。

### 7.2 使用 `rsync` 进行文件级备份

如果你更倾向于文件级备份，可以使用 `rsync`。

```bash
rsync -aAXv /mnt/@ /path/to/backup/root/
rsync -aAXv /mnt/home/@ /path/to/backup/home/
```

**选项解释**：

- `-a`：归档模式，保持符号链接、权限、时间戳等。
- `-A`：保留 ACL（访问控制列表）。
- `-X`：保留扩展属性。
- `-v`：详细输出。

### 7.3 使用备份工具

除了 `btrfs send` 和 `rsync`，还有其他备份工具如 **btrbk**、**backy2**，它们提供更多功能和自动化选项。

## 8. 最佳实践与注意事项

### 8.1 定期创建快照

- **频率**：根据使用情况定期创建快照，如每日、每周。
- **清理**：定期删除过期或不需要的快照，以节省空间。

### 8.2 管理子卷空间

- **平衡（Balance）**：定期进行文件系统平衡，优化空间分配。

  ```bash
  sudo btrfs balance start /mnt
  ```

- **碎片整理（Defragmentation）**：对频繁修改的小文件启用自动碎片整理。

  ```bash
  sudo btrfs filesystem defragment -r -v /mnt
  ```

### 8.3 挂载选项优化

根据使用场景调整挂载选项，以提升性能和耐用性。

- **压缩**：启用 `compress=zstd` 提高存储效率。
- **空间缓存**：启用 `space_cache` 加快文件系统操作。
- **自动碎片整理**：对于 SSD，启用 `autodefrag` 提升性能。

### 8.4 监控文件系统健康

使用 `btrfs scrub` 定期检查文件系统的完整性。

```bash
sudo btrfs scrub start /mnt
sudo btrfs scrub status /mnt
```

### 8.5 备份策略

- **多地点备份**：将备份存储在不同的物理位置，防止数据丢失。
- **版本控制**：保留多个快照版本，以便在不同时间点恢复。

### 8.6 注意子卷限制

- **不支持嵌套子卷**：子卷内不应创建其他子卷，除非有明确需求。
- **挂载层次**：避免子卷间的复杂挂载层次，简化管理。

## 9. 总结

**btrfs 子卷** 是一个强大且灵活的工具，允许你在单一文件系统内创建多个独立的文件系统实例。通过合理利用子卷，你可以：

- **优化系统结构**：将系统文件与用户数据分离，提高系统性能和安全性。
- **简化备份与恢复**：快速创建一致性的快照，便于数据备份和系统恢复。
- **提升管理效率**：通过子卷隔离不同的数据集，简化文件系统管理。

在你的 Arch Linux 配置中，将 `/` 和 `/efi` 挂载在 SSD 上，`/home` 挂载在 HDD 上，并使用子卷进行组织，是一种高效且实用的方案。通过遵循本文提供的步骤和最佳实践，你可以充分发挥 btrfs 的优势，打造一个稳定、高性能的 Linux 系统环境。

如果你在实施过程中遇到任何问题或需要进一步的帮助，请随时提问！
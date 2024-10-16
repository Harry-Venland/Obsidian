安装 Arch Linux 并将一个机械硬盘（HDD）和一个固态硬盘（SSD）组合成 btrfs 文件系统，其中 `/` 和 `/efi` 挂载在 SSD 上，`/home` 挂载在 HDD 上，是一个有效的配置，可以平衡系统性能和存储容量。以下是详细的步骤指导，帮助你完成这一安装过程。

## 目录

1. [前提条件](#1-前提条件)
2. [准备安装介质](#2-准备安装介质)
3. [启动至 Arch Linux 安装环境](#3-启动至-arch-linux-安装环境)
4. [磁盘分区](#4-磁盘分区)
5. [格式化分区](#5-格式化分区)
6. [挂载分区](#6-挂载分区)
7. [安装基本系统](#7-安装基本系统)
8. [配置系统](#8-配置系统)
9. [安装和配置引导加载器](#9-安装和配置引导加载器)
10. [完成安装并重启](#10-完成安装并重启)
11. [后续配置](#11-后续配置)
12. [常见问题排查](#12-常见问题排查)

---

## 1. 前提条件

### 硬件要求

- **台式电脑或笔记本**：配备一个 SSD 和一个 HDD。
- **USB 闪存盘**：用于创建 Arch Linux 安装介质。
- **网络连接**：建议有稳定的互联网连接以下载必要的软件包。

### 软件要求

- **Arch Linux ISO**：从 [Arch Linux 官方网站](https://archlinux.org/download/) 下载最新的 Arch Linux ISO 镜像。
- **工具**：用于创建启动 USB，如 **Rufus**（Windows）、**Etcher**（跨平台）或 **dd**（Linux）。

### 其他要求

- **备份数据**：确保在操作前备份所有重要数据，以防数据丢失。

---

## 2. 准备安装介质

1. **下载 Arch Linux ISO**：

   前往 [Arch Linux 下载页面](https://archlinux.org/download/) 下载最新的 ISO 文件。

2. **创建启动 USB**：

   使用适当的工具将 ISO 写入 USB 闪存盘。

   - **使用 Rufus（Windows）**：
     1. 插入 USB 闪存盘。
     2. 打开 Rufus，选择下载的 ISO 文件。
     3. 选择 USB 设备，点击“开始”。
   
   - **使用 Etcher（跨平台）**：
     1. 打开 Etcher。
     2. 选择下载的 ISO 文件。
     3. 选择目标 USB 设备，点击“Flash”。

   - **使用 dd（Linux）**：
     ```bash
     sudo dd if=/path/to/archlinux.iso of=/dev/sdX bs=4M status=progress && sync
     ```
     **注意**：将 `/dev/sdX` 替换为你的 USB 设备路径（例如 `/dev/sdb`），谨慎操作以避免覆盖错误的磁盘。

---

## 3. 启动至 Arch Linux 安装环境

1. **插入启动 USB**：

   将创建好的 Arch Linux 启动 USB 插入目标计算机。

2. **启动计算机并进入 BIOS/UEFI 设置**：

   根据主板或笔记本的不同，通常通过按 `F2`、`F10`、`DEL` 或其他键进入 BIOS/UEFI 设置。

3. **设置从 USB 启动**：

   在 BIOS/UEFI 中设置 USB 设备为首选启动项，保存并重启。

4. **进入 Arch Linux 安装环境**：

   计算机应从 USB 启动并进入 Arch Linux 命令行环境。

---

## 4. 磁盘分区

假设：

- **SSD**：`/dev/sda`
- **HDD**：`/dev/sdb`

**注意**：实际设备名称可能不同。使用 `lsblk` 或 `fdisk -l` 确认设备名称。

### 4.1 确认磁盘

```bash
lsblk
```

确认 SSD 和 HDD 的设备名称（例如 `/dev/sda` 和 `/dev/sdb`）。

### 4.2 分区 SSD（/ 和 /efi）

使用 `gdisk` 或 `fdisk` 进行分区。以下示例使用 `gdisk`。

```bash
# 安装 gdisk（如果未安装）
pacman -Sy gdisk

# 启动 gdisk 进行分区
gdisk /dev/sda
```

在 `gdisk` 中：

1. 输入 `o` 创建一个新的 GPT 分区表。
2. 输入 `n` 创建 EFI 分区：
   - 分区号：默认
   - 起始扇区：默认
   - 结束扇区：+512M
   - 类型：EF00（EFI System Partition）
3. 输入 `n` 创建根分区：
   - 分区号：默认
   - 起始扇区：默认
   - 结束扇区：整个剩余空间
   - 类型：8300（Linux filesystem）
4. 输入 `w` 保存并退出。

### 4.3 分区 HDD（/home）

同样使用 `gdisk`：

```bash
gdisk /dev/sdb
```

在 `gdisk` 中：

1. 输入 `o` 创建一个新的 GPT 分区表。
2. 输入 `n` 创建一个分区：
   - 分区号：默认
   - 起始扇区：默认
   - 结束扇区：整个磁盘
   - 类型：8300（Linux filesystem）
3. 输入 `w` 保存并退出。

---

## 5. 格式化分区

### 5.1 格式化 EFI 分区

```bash
mkfs.fat -F32 /dev/sda1
```

### 5.2 格式化根分区和 /home 分区为 btrfs

```bash
mkfs.btrfs -f /dev/sda2  # 根分区
mkfs.btrfs -f /dev/sdb1  # /home 分区
```

**选项说明**：

- `-f`：强制格式化，即使分区上已有数据。

### 5.3 检查文件系统

```bash
blkid
```

确认每个分区的文件系统类型和 UUID。

---

## 6. 挂载分区

### 6.1 挂载根分区

```bash
mount /dev/sda2 /mnt
```

### 6.2 创建并挂载 EFI 分区

```bash
mkdir -p /mnt/efi
mount /dev/sda1 /mnt/efi
```

### 6.3 创建并挂载 /home 分区

```bash
mkdir -p /mnt/home
mount /dev/sdb1 /mnt/home
```

**注意**：`/home` 是一个单独的分区，因此需要单独挂载。

---

## 7. 安装基本系统

### 7.1 更新镜像服务器

编辑 `/etc/pacman.d/mirrorlist`，选择最快的镜像服务器。可以使用 `reflector` 工具自动排序镜像：

```bash
pacman -Sy reflector
reflector --verbose --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
```

### 7.2 安装基本包

```bash
pacstrap /mnt base linux linux-firmware btrfs-progs
```

**说明**：

- `base`：基本系统包。
- `linux`：Linux 内核。
- `linux-firmware`：固件包。
- `btrfs-progs`：btrfs 工具。

### 7.3 生成 fstab

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

**说明**：

- `-U`：使用 UUID。
- 确保 `/etc/fstab` 包含正确的挂载信息。

---

## 8. 配置系统

### 8.1 进入新系统环境

```bash
arch-chroot /mnt
```

### 8.2 设置时区

```bash
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc
```

**示例**：

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

### 8.3 设置本地化

编辑 `/etc/locale.gen`，取消 `en_US.UTF-8 UTF-8` 和其他需要的语言。

```bash
nano /etc/locale.gen
```

生成 locale：

```bash
locale-gen
```

创建 `/etc/locale.conf`：

```bash
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

### 8.4 设置主机名

编辑 `/etc/hostname`：

```bash
echo "myarch" > /etc/hostname
```

编辑 `/etc/hosts`：

```bash
nano /etc/hosts
```

添加以下内容：

```
127.0.0.1    localhost
::1          localhost
127.0.1.1    myarch.localdomain myarch
```

### 8.5 设置 root 密码

```bash
passwd
```

### 8.6 创建普通用户

```bash
useradd -m -G wheel -s /bin/bash yourusername
passwd yourusername
```

编辑 sudoers 文件以允许 wheel 组使用 sudo：

```bash
EDITOR=nano visudo
```

取消以下行的注释：

```
%wheel ALL=(ALL) ALL
```

---

## 9. 安装和配置引导加载器

### 9.1 安装 GRUB 和 EFI 工具

```bash
pacman -S grub efibootmgr
```

### 9.2 安装 GRUB 到 EFI 分区

```bash
mkdir -p /boot/efi
mount /dev/sda1 /boot/efi
```

### 9.3 安装 GRUB

```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
```

### 9.4 配置 GRUB

生成 GRUB 配置文件：

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

### 9.5 确认引导加载器

确保 GRUB 已正确安装，可以重启并检查是否能够进入 GRUB 菜单。

---

## 10. 完成安装并重启

1. **退出 chroot 环境**：

   ```bash
   exit
   ```

2. **卸载分区**：

   ```bash
   umount -R /mnt
   ```

3. **重启系统**：

   ```bash
   reboot
   ```

4. **移除 USB 安装盘**，让系统从 SSD 启动。

---

## 11. 后续配置

### 11.1 配置 fstab

确保 `/etc/fstab` 包含以下内容：

```fstab
# /etc/fstab: static file system information.
#
# <file system> <mount point> <type> <options> <dump> <pass>
UUID=your-ssd-root-uuid / btrfs defaults,noatime,compress=zstd,space_cache 0 0
UUID=your-ssd-efi-uuid /efi vfat defaults,noatime 0 0
UUID=your-hdd-home-uuid /home btrfs defaults,noatime,compress=zstd,space_cache 0 0
```

**获取 UUID**：

```bash
blkid
```

### 11.2 优化 btrfs 挂载选项

编辑 `/etc/fstab` 以添加挂载选项：

- `compress=zstd`：启用 Zstandard 压缩。
- `noatime`：禁用访问时间记录，提升性能。
- `space_cache`：启用空间缓存，提升性能。

### 11.3 安装必要的软件包

安装常用软件包，如网络工具、文本编辑器等：

```bash
pacman -S networkmanager vim sudo
```

启用 NetworkManager 服务：

```bash
systemctl enable NetworkManager
```

### 11.4 配置 btrfs 子卷（可选）

如果希望使用 btrfs 子卷来管理不同的数据集，可以创建子卷。例如，为 `/` 和 `/home` 创建子卷：

```bash
# 为根文件系统创建子卷
btrfs subvolume create /@

# 为 /home 创建子卷
btrfs subvolume create /@home
```

更新 `/etc/fstab` 以挂载子卷：

```fstab
UUID=your-ssd-root-uuid / btrfs defaults,subvol=@,compress=zstd,space_cache 0 0
UUID=your-hdd-home-uuid /home btrfs defaults,subvol=@home,compress=zstd,space_cache 0 0
UUID=your-ssd-efi-uuid /efi vfat defaults,noatime 0 0
```

**注意**：确保在子卷创建后正确挂载。

### 11.5 启用 TRIM（对于 SSD）

编辑 `/etc/fstab`，为 SSD 分区添加 `discard` 选项以启用 TRIM：

```fstab
UUID=your-ssd-root-uuid / btrfs defaults,subvol=@,compress=zstd,space_cache,discard 0 0
```

或者，设置定期 TRIM 调度任务：

```bash
systemctl enable fstrim.timer
```

### 11.6 优化内核参数（可选）

根据需要，可以调整内核参数以优化性能，例如启用 `autodefrag`：

```fstab
UUID=your-ssd-root-uuid / btrfs defaults,subvol=@,compress=zstd,space_cache,autodefrag 0 0
```

---

## 12. 常见问题排查

### 12.1 无法启动进入系统

- **检查引导加载器**：确保 GRUB 正确安装并配置。
- **检查分区挂载**：确保 `/efi` 分区正确挂载，并在 GRUB 配置中指定正确的 EFI 分区。

### 12.2 文件系统挂载失败

- **检查 UUID**：确保 `/etc/fstab` 中的 UUID 正确无误。
- **检查分区类型**：确认每个分区的文件系统类型与 `/etc/fstab` 中指定的一致。

### 12.3 网络连接问题

- **启用 NetworkManager**：

  ```bash
  systemctl enable NetworkManager
  systemctl start NetworkManager
  ```

- **检查网络配置**：

  使用 `ip a` 或 `ping` 命令测试网络连接。

### 12.4 btrfs 子卷未正确挂载

- **确认子卷创建**：

  ```bash
  btrfs subvolume list /
  ```

- **检查 `/etc/fstab` 配置**：确保子卷名称正确，并重新挂载文件系统。

---

## 结论

通过上述步骤，你可以成功地在 Arch Linux 上配置一个由 SSD 和 HDD 组成的 btrfs 文件系统，其中 `/` 和 `/efi` 挂载在 SSD 上，`/home` 挂载在 HDD 上。这种配置能够有效地利用 SSD 的高速性能来提升系统响应速度，同时利用 HDD 的大容量存储来保存用户数据。

**提示**：

- **定期备份**：尽管 btrfs 提供了一些数据保护功能，定期备份仍然至关重要。
- **更新系统**：定期运行 `pacman -Syu` 以保持系统和软件包的最新状态。
- **监控系统健康**：使用 btrfs 的 `scrub` 和 `balance` 命令定期检查和优化文件系统。

如果在安装过程中遇到任何问题或需要进一步的帮助，请随时提出！
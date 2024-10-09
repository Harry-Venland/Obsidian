### 1. 什么是 nftables？

`nftables` 是 Linux 内核中的一个框架，用于设置、维护和检查网络流量过滤规则。它旨在替代 iptables、ip6tables、arptables 和 ebtables，提供一个统一的接口，支持 IPv4、IPv6、ARP 和以太网流量过滤。

### 2. 安装 nftables

在大多数现代 Linux 发行版上，`nftables` 通常已经包含在内核中。你可以通过以下命令安装 `nftables` 工具：

- **Debian/Ubuntu**:
  ```bash
  sudo apt update
  sudo apt install nftables
  ```

- **CentOS/RHEL**:
  ```bash
  sudo yum install nftables
  ```

### 3. 启动和启用 nftables 服务

在大多数系统上，`nftables` 是作为服务运行的。你可以使用以下命令启动并启用它：

```bash
sudo systemctl start nftables
sudo systemctl enable nftables
```

### 4. 基本命令

- **查看当前规则**:
  ```bash
  sudo nft list ruleset
  ```

- **添加规则**:
  ```bash
  sudo nft add rule <table> <chain> <rule>
  ```

- **删除规则**:
  ```bash
  sudo nft delete rule <table> <chain> handle <handle>
  ```

- **删除整个链**:
  ```bash
  sudo nft delete chain <table> <chain>
  ```

- **清空所有规则**:
  ```bash
  sudo nft flush ruleset
  ```

### 5. nftables 基础概念

- **表（Table）**: 规则集合的容器，可以将不同类型的流量进行分类。
- **链（Chain）**: 规则的有序集合，流量在处理时会依次经过这些规则。
- **规则（Rule）**: 定义流量的匹配条件和处理动作。

### 一、四表五链的结构

#### 1. 四表（Table）

在 `nftables` 中，通常使用四个表来组织规则：

- **filter**：用于过滤网络流量，通常用于防火墙。
- **nat**：用于网络地址转换，例如 SNAT 和 DNAT。
- **mangle**：用于修改数据包的内容，如更改 TOS（服务类型）和 TTL（生存时间）。
- **raw**：用于进行更低层次的处理，主要用于绕过连接跟踪。

#### 2. 五链（Chain）

每个表中可以包含多个链。每个链的处理顺序和策略可以不同。五个常见的链包括：

- **input**：处理进入本机的流量。
- **output**：处理从本机发出的流量。
- **forward**：处理经过本机转发的流量。
- **prerouting**：在数据包路由决策之前处理流量，通常用于 DNAT。
- **postrouting**：在数据包离开本机之前处理流量，通常用于 SNAT。

链表对应关系

| 链名          | 对应的表名                 |
| :---------- | :-------------------- |
| INPUT       | mangle、nat、filter     |
| OUTPUT      | raw、mangle、Nat、filter |
| FORWARD     | mangle、filter         |
| PREROUTING  | raw、mangle、nat        |
| POSTROUTING | mangle、nat            |

记忆技巧：进出第一关的链都没有fileter表，第一个进链除fileter都包含，input除raw都有、output全有、出链只有mangle和nat、forward只有mongle和filter

### 二、规则（Rule）

规则是用于匹配和处理流量的具体指令。规则可以指定条件（如源地址、目标地址、协议、端口等）和相应的动作（如接受、拒绝、丢弃等）。

### 三、详细讲解

#### 1. 表的创建和链的添加

创建表和链的基本命令如下：

```bash
# 创建一个 filter 表
nft add table ip filter

# 创建一个 input 链
nft add chain ip filter input { type filter hook input priority 0; }

# 创建一个 output 链
nft add chain ip filter output { type filter hook output priority 0; }

# 创建一个 forward 链
nft add chain ip filter forward { type filter hook forward priority 0; }
```

在这里，`type filter` 指定了链的类型，`hook input` 表示此链用于处理输入流量，`priority 0` 指定处理优先级，数字越小优先级越高。

#### 2. 规则的添加

添加规则的基本命令如下：

```bash
# 允许来自 192.168.1.0/24 网段的流量
nft add rule ip filter input ip saddr 192.168.1.0/24 accept

# 拒绝所有其他输入流量
nft add rule ip filter input drop
```

#### 3. NAT 表的使用

NAT 表的主要功能是实现地址转换，常用的有 DNAT 和 SNAT。

**DNAT**（目的地址转换）：

```bash
# 创建 NAT 表
nft add table ip nat

# 创建 prerouting 链
nft add chain ip nat prerouting { type nat hook prerouting priority -100; }

# 将所有到达 80 端口的流量转发到内网 192.168.1.100
nft add rule ip nat prerouting tcp dport 80 dnat to 192.168.1.100
```

**SNAT**（源地址转换）：

```bash
# 创建 postrouting 链
nft add chain ip nat postrouting { type nat hook postrouting priority 100; }

# 将从 192.168.1.0/24 网段发出的流量的源地址转换为 203.0.113.1
nft add rule ip nat postrouting ip saddr 192.168.1.0/24 snat to 203.0.113.1
```

#### 4. Mangle 表的使用

Mangle 表主要用于修改数据包的内容，例如更改 TOS 和 TTL：

```bash
# 创建 mangle 表
nft add table ip mangle

# 创建 input 链
nft add chain ip mangle input { type filter hook input priority 0; }

# 设置 TTL
nft add rule ip mangle input ip ttl set 64
```

### 四、查看和管理规则

- 查看当前的规则集：
  ```bash
  nft list ruleset
  ```

- 删除规则：
  ```bash
  nft delete rule ip filter input handle <handle>
  ```

- 删除整个链：
  ```bash
  nft delete chain ip filter input
  ```

- 清空规则：
  ```bash
  nft flush ruleset
  ```

### 五、示例规则集

以下是一个完整的 `nftables` 规则集示例，结合了四个表和五个链的使用：

```bash
# 创建过滤表
nft add table ip filter

# 创建链
nft add chain ip filter input { type filter hook input priority 0; }
nft add chain ip filter output { type filter hook output priority 0; }
nft add chain ip filter forward { type filter hook forward priority 0; }

# 添加规则
nft add rule ip filter input ip saddr 192.168.1.0/24 accept
nft add rule ip filter input ct state established,related accept
nft add rule ip filter input tcp dport 22 accept
nft add rule ip filter input drop

# 创建 NAT 表
nft add table ip nat
nft add chain ip nat prerouting { type nat hook prerouting priority -100; }
nft add chain ip nat postrouting { type nat hook postrouting priority 100; }

# 添加 NAT 规则
nft add rule ip nat prerouting tcp dport 80 dnat to 192.168.1.100
nft add rule ip nat postrouting ip saddr 192.168.1.0/24 snat to 203.0.113.1
```

### 六、总结

理解 `nftables` 中的四表五链和规则结构是掌握网络流量过滤的关键。通过合理地配置表、链和规则，可以实现强大的网络安全策略和流量管理。

### 6. 示例

#### 6.1 创建一个简单的规则集

1. 创建一个表：
   ```bash
   sudo nft add table ip filter
   ```

2. 创建一个链：
   ```bash
   sudo nft add chain ip filter input { type filter hook input priority 0; }
   ```

3. 添加规则：
   - 允许 SSH 流量（端口 22）：
     ```bash
     sudo nft add rule ip filter input tcp dport 22 accept
     ```

   - 拒绝所有其他流量：
     ```bash
     sudo nft add rule ip filter input drop
     ```

4. 查看规则：
   ```bash
   sudo nft list ruleset
   ```

#### 6.2 使用状态匹配

允许已经建立的连接的流量：

```bash
sudo nft add rule ip filter input ct state established,related accept
```

#### 6.3 创建 NAT 规则

1. 创建 NAT 表：
   ```bash
   sudo nft add table ip nat
   ```

2. 创建 POSTROUTING 链：
   ```bash
   sudo nft add chain ip nat postrouting { type nat hook postrouting priority 100; }
   ```

3. 添加 SNAT 规则：
   ```bash
   sudo nft add rule ip nat postrouting oifname "eth0" snat to 192.168.1.1
   ```

### 7. 保存和加载规则

在某些 Linux 发行版中，`nftables` 规则在重启后不会自动加载。你可以手动保存和加载规则：

- **保存规则**：
  ```bash
  sudo nft list ruleset > /etc/nftables.conf
  ```

- **加载规则**：
  ```bash
  sudo nft -f /etc/nftables.conf
  ```

### 8. 参考资料

- [nftables 官方文档](https://netfilter.org/projects/nftables/index.html)
- [nftables wiki](https://wiki.nikiv.dev/)

### 总结

`nftables` 是一个强大的工具，能够更好地管理 Linux 系统的网络流量和安全性。
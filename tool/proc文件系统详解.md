# `/proc`文件系统讲解

`/proc`文件系统是一个虚拟文件系统，提供了对正在运行中的内核和系统信息的动态访问。它不包含任何磁盘文件，而是由内核在内存中动态生成的。

## `/proc`文件系统概述

`/proc`文件系统提供了一些内核中各个子系统的信息，它使得用户空间地址简单地使用cat和echo命令，或者read和write系统调用就可以获得各子系统的信息，比如CPU型号和参数、内存使用量、可用的定时硬件及详细参数、连接的外部设备及映射地址等等，还可以在系统运行时动态修改内核参数，而不需要重新编译内核源代码。

`proc`文件系统的挂载点是`/proc`，它最早设计用于提供进程运行时的信息，比如进程的运行状态、进程当前打开的文件、创建的套接字、虚拟内存的排布等，这也是它名字的由来(Process Data Filesystem)。后来很多的系统及内核信息也被加入进来，如中断信息、设备映射信息、内存状态等。

```bash
mount                                               
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=8074644k,nr_inodes=2018661,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,noexec,relatime,size=1628232k,mode=755)
```

## 下面是/proc文件系统中一些常见的信息

- /proc/cpuinfo：显示处理器的信息，如型号、频率、缓存大小等。

```bash
cat /proc/cpuinfo   
processor       : 0
vendor_id       : GenuineIntel
cpu family      : 6
model           : 165
model name      : Intel(R) Core(TM) i7-10700 CPU @ 2.90GHz
stepping        : 5
microcode       : 0xfc
cpu MHz         : 4599.702
cache size      : 16384 KB
physical id     : 0
siblings        : 16
core id         : 0
cpu cores       : 8
apicid          : 0
initial apicid  : 0
fpu             : yes
fpu_exception   : yes
cpuid level     : 22
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid_fault epb ssbd ibrs ibpb stibp ibrs_enhanced tpr_shadow flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid mpx rdseed adx smap clflushopt intel_pt xsaveopt xsavec xgetbv1 xsaves dtherm ida arat pln pts vnmi pku ospke md_clear flush_l1d arch_capabilities
vmx flags       : vnmi preemption_timer posted_intr invvpid ept_x_only ept_ad ept_1gb flexpriority apicv tsc_offset vtpr mtf vapic ept vpid unrestricted_guest vapic_reg vid ple shadow_vmcs pml ept_mode_based_exec
bugs            : spectre_v1 spectre_v2 spec_store_bypass swapgs itlb_multihit srbds mmio_stale_data retbleed eibrs_pbrsb gds bhi
bogomips        : 5799.77
clflush size    : 64
cache_alignment : 64
address sizes   : 39 bits physical, 48 bits virtual
power management:
```

- /proc/meminfo：提供内存的详细信息，包括总内存、空闲内存、缓存和交换空间的使用情况等。

```bash
cat /proc/meminfo          
MemTotal:       16282292 kB
MemFree:         1221952 kB
MemAvailable:    7376704 kB
Buffers:          355280 kB
Cached:          5865704 kB
SwapCached:       630192 kB
Active:          5170492 kB
Inactive:        6554236 kB
Active(anon):    3913884 kB
Inactive(anon):  1754152 kB
Active(file):    1256608 kB
Inactive(file):  4800084 kB
Unevictable:          80 kB
Mlocked:              80 kB
SwapTotal:      16777212 kB
SwapFree:       14625276 kB
Zswap:                 0 kB
Zswapped:              0 kB
Dirty:              1636 kB
Writeback:             0 kB
AnonPages:       5454320 kB
Mapped:          1346012 kB
Shmem:            164284 kB
KReclaimable:    1188764 kB
Slab:            1518844 kB
SReclaimable:    1188764 kB
SUnreclaim:       330080 kB
KernelStack:       43536 kB
PageTables:       111208 kB
SecPageTables:         0 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:    24918356 kB
Committed_AS:   47798212 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       91648 kB
VmallocChunk:          0 kB
Percpu:            17472 kB
HardwareCorrupted:     0 kB
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
FileHugePages:      2048 kB
FilePmdMapped:         0 kB
CmaTotal:              0 kB
CmaFree:               0 kB
Unaccepted:            0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
Hugetlb:               0 kB
DirectMap4k:     2675084 kB
DirectMap2M:    14024704 kB
DirectMap1G:     1048576 kB
```

- /proc/kallsyms：包含内核的所有全局变量和函数在内存中的地址。系统崩溃时产生opps信息中，函数调用堆栈中显示出来的函数名，就是在这个文件的帮助下生成的

- /proc/interrupts：包含系统记录的每个CPU上处理的各类终端信息。irqbalance可以帮助系统把中断分发给不同的CPU，实现负载均衡

- /proc/net/：包含网络相关信息，如/proc/net/tcp显示TCP连接列表，/proc/net/dev显示网络设备统计信息等。

  > - `/proc/net/tcp`: 显示TCP连接的统计信息  
    > - `/proc/net/udp`: 显示UDP连接的统计信息
    > - `/proc/net/dev`: 显示网络设备的使用情况

- /proc/sys/：包含内核参数的目录，可以通过修改这些文件来动态地调整内核的行为，例如/proc/sys/net/ipv4/tcp_keepalive_time用于设置TCP keepalive时间。

- /proc/loadavg：显示系统负载平均值，包括1分钟、5分钟和15分钟的负载情况。前面的值比后面的值小，说明系统负载在减轻；反之，说明系统负载呈现上升趋势

- /proc/version：显示正在运行的内核版本、gcc版本等信息。

- /proc/PID/：每个进程都有一个以其PID命名的目录，包含了该进程的详细信息

|文件/目录|作用|示例命令|
|:-|:-|:-|
|`cmdline`|进程启动命令及参数|`cat /proc/1234/cmdline`|
|`status`|进程状态(内存、PID、用户等)|`cat /proc/1234/status`|
|`fd/`|进程打开的文件描述符|`ls -l /proc/1234/fd`|
|`maps`|进程内存映射(堆栈、库等)|`cat /proc/1234/maps`|

- /proc/filesystem: 列出当前内核支持的文件系统类型

- /proc/modules：列出已加载的内核模块。等同`lsmod`命令

通过读取和修改这些文件，可以实时监控系统状态、调整系统参数以及了解进程的详细信息，是系统调试和性能优化中的重要工具。

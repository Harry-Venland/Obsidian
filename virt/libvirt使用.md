## libvirt使用

```shell

systemctl start libvirt # 服务启动

qemu-img create -f qcow2/raw xxx.qcow2/raw 50G # 创建磁盘格式为qcow2/raw(qcow2慢慢填充，raw直接占用满)

qemu-img resize xxx.qcow2/raw +xxG # 磁盘扩容(需要先关闭虚拟机确认磁盘不在占用)

qemu-img info xxx.qcow2/raw # 查看磁盘信息

  

wget https://xxx*(cui/gui)-(uefi/legacy)*xxx # 获取镜像包(有无操作界面)(磁盘启动方式)

# 镜像包位置 http://10.7.60.100/euler/fuyu/daily_master/xxxx (x86_64代表intel)

  

xz -d -T 0 -v xxx # 解压镜像包

mv xxx.xxx xxxx.xxx # 重命名镜像包为xml文件里定义的名字

virsh create vm.xml # 启动(需要先new/cp一个xml文件,并做对应修改)

virsh vncdisplay VM-NAME # 查询vnc端口

vncviewer ipaddress:port # 连接虚拟机

# 获取repo源配置 去OBS获取版本对应的repo源配置

  

virsh destroy VM-NAME # 关闭虚拟机(destory等同于强制重启,不建议直接使用)

virsh dompmwakeup id # 唤醒虚拟机，当虚拟机状态为pmsuspended，id为virsh list查看的id

```
## 忘记用户密码

- 重启 在grub页面按e
- 在倒数第二行中讲ro改为rw，在行尾添加init /bin/sh
- 按ctrl+x退出重启，进入单用户模式，执行"passwd 用户" 修改密码
- 如果开启SElinux，输入如下命令：touch /.autorelabel
- 修改完成后重启"exec /sbin/init"

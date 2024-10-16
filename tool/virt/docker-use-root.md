#### docker挂载iso

- 超级模式运行(赋予完全root权限)

```shell

docker run -itd --privileged=true troll/centos7.6:0.0.1

```

- 登录容器

```shell

docker exec -it 7a3637f7a3ae /usr/bin/bash

```

- 新建目录

```shell

mkdir /mnt/cdrom

```

- 挂载iso

```shell

mount -o loop /mnt/iso/xxx.iso /mnt/cdrom

```
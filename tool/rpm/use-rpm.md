#### rpm-build 中dist定义

`cat /etc/rpm/macros.dist`
#### rpm macros 

`/usr/lib/rpm/macros`
`/usr/lib/rpm/macros.d/`

#### rpm源码包解压

```shell
rpm2cpio *.src.rpm | cpio -iv
```

#### rpm查看某个包内容

```shell
rpm -qlp xxx.rpm #quire list Package
```

#### 查看包依赖关系

```shell
repoquery --provides xxx 查看提供了哪些rpm文件
repoquery --whatprovides xxx 查看那个rpm提供这个文件
```

#### rpm 依赖包查询

```shell
rpm -qR xxx
```

#### 挂载本地镜像为repo源

- 创建对应目录

```shell

mkdir -p /media/root/xxOS

mount /dev/sr0 /media/root/xxOS

```

- repo文件内容

```shell

[iso-repo]

name=ISO-REPO

baseurl=file:///media/root/xxOS

enabled=1

gpgcheck=0

```
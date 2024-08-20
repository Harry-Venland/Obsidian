# koji基本用法

## 管理员操作

- add_tag

```bash
koji add-tag dist-foo
```

- add arch
```bash
koji add-tag --parent dist-foo --arches "x86_64" dist-foo-build
```

- add target

```bash
koji add-target dist-foo dist-foo-build
```

- add group

```bash
koji add-group-pkg 105xe-build build bash bzip2 coreutils cpio diffutils findutils gawk gcc gcc-c++ grep gzip info make patch rpm-build scl-utils-build sed shadow-utils tar unzip util-linux which xz git setup
koji add-group-pkg 105xe-build srpm-build bash git rpm-build scl-utils-build shadow-utils system-release 
```
    

- regen repo

```bash
koji regen-repo dist-foo-build
```

- 添加外部源

```bash
koji add-external-repo -t dist-foo-build dist-foo-external-devplus http://10.7.10.216:82/devplus/
koji add-external-repo -t dist-foo-build dist-foo-external-baseos http://10.7.10.216:82/baseos/
koji add-external-repo -t dist-foo-build dist-foo-external-appstream http://10.7.10.216:82/appstream/
koji add-external-repo -t dist-foo-build dist-foo-external-powertools http://10.7.10.216:82/powertools/
koji add-external-repo -t dist-foo-build dist-foo-external-epel http://10.7.10.216:82/epel/
koji add-external-repo -t dist-foo-build dist-foo-external-extra http://10.7.10.216:82/extra/
```

- regen repo

```bash
koji regen-repo dist-foo-build
```

- 生成mock配置

```bash
koji mock-config --tag dist-foo-build --arch=x86_64 --topurl=https://kojidev.example.com/kojifiles                   dist-foo
```

- 加包到tag


```bash
koji add-pkg --owner=kdreyer dist-foo ModemManager
```



- build

```bash
koji build dist-foo  'git+ssh://git@gitlabxa.uniontech.com/server/enterprisec/cloud/sshpass.git?#HEAD'
```


```bash
koji --server="http://10.30.38.102/kojihub" --weburl="http://10.30.38.102/koji" --topurl="http://10.30.38.102/kojifiles" --user  kojiadmin --password adminkoji build c8 --scratch /home/tyq/SM/rpmbuild/SRPMS/openssl-1.1.1g-15.uelc20.06.src.rpm --nowait &> /dev/null
```


## 开发者使用

1. 添加软件包到指定tag

将`bash`添加到`105xe` tag

```bash
koji add-pkg --owner wangqing 105xe bash
```

2. 编译软件包 - release 构建

```bash
koji build 105xe bash-5.0-17.up1.uel20.src.rpm --nowait
```

3. 编译软件包 - 个人构建

```bash
koji build 105xe bash-5.0-17.up1.uel20.src.rpm --scratch --nowait 
```
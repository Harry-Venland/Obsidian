# koji基本用法

## 管理员操作

- add tag

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
koji add-group-pkg aa-tag-build build bash bzip2 coreutils cpio diffutils findutils gawk gcc gcc-c++ grep gzip info make patch rpm-build scl-utils-build sed shadow-utils tar unzip util-linux which xz git setup
koji add-group-pkg aa-tag-build srpm-build bash git rpm-build scl-utils-build shadow-utils system-release 
```
    

- regen repo

```bash
koji regen-repo dist-foo-build
```

- 添加外部源

```bash
koji add-external-repo -t dist-foo-build dist-foo-external-devplus http://xx/devplus/
koji add-external-repo -t dist-foo-build dist-foo-external-baseos http://xx/baseos/
koji add-external-repo -t dist-foo-build dist-foo-external-appstream http://xx/appstream/
koji add-external-repo -t dist-foo-build dist-foo-external-powertools http://xx/powertools/
koji add-external-repo -t dist-foo-build dist-foo-external-epel http://xx/epel/
koji add-external-repo -t dist-foo-build dist-foo-external-extra http://xx/extra/
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
koji --user xx --password xx build aa-tag git+http://xx/aa-name#origin/branch
```

```bash
koji --server="http://xx/kojihub" --weburl="http://xx/koji" --topurl="http://xx/kojifiles" --user  kojiadmin --password adminkoji build aa-tag --scratch xx.src.rpm --nowait &> /dev/null
```

## 开发者使用

1. 添加软件包到指定tag

将`bash`添加到`aa-tag` tag

```bash
koji add-pkg --owner aa aa-tag bash
```

2. 编译软件包 - release 构建

```bash
koji build aa-tag xx.src.rpm --nowait
```

3. 编译软件包 - 个人构建

```bash
koji build aa-tag xx.src.rpm --scratch --nowait 
```

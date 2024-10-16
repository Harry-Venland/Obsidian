## openEuler配置osc

### 前提条件

需要root权限，已设置openEuler的repo软件源

### 操作步骤

- 安装osc命令行及依赖

```shell
yum install osc build
```

- 配置osc

```shell
cat ~/.config/osc/oscrc
[general]
apiurl=https://build.openeuler.openatom.cn/
no_verify=1   #跳过GPG校验
[https://build.openeuler.openatom.cn/]
user=name
pass=password
```

### 一些基础命令

- 拉取已有工程

```shell
osc co xxxx/xx
```

- 同步远程代码到本地

```shell
osc up -S
```

- 构建RPM包

```shell
rm -f _service;for file in `ls | grep -v .osc`;do new_file=${file##*:};mv $file $new_file;done

```

- 更新文件

```shell
osc ar
```

- 提交修改到OBS服务器

```shell
osc ci -m "xx"
```

[openEuler参考](https://docs.openeuler.org/zh/docs/22.03_LTS_SP2/docs/ApplicationDev/%E6%9E%84%E5%BB%BARPM%E5%8C%85.html)

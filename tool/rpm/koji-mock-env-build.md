## 基于koji的mock环境搭建

### 拉取koji某个tag的配置文件  
```bash
koji mock-config -a x86_64 --tag=xxx-build --target=xxx --latest >> xxx.cfg
```
参数解释  
- -a 系统架构  
- -tag koji上build名称
- -target koji上tag名称
- --latest 获取repo上最新包组 

### 修改配置文件进行网络连接和host解析  
```txt
config_opts['rpmbuild_networking'] = True  
config_opts['use_host_resolv'] = True
```

### 添加其他repo
添加同时需要添加优先级，本地repo优先级高于之前repo优先级
```shell
config_opts['yum.conf'] = '[main]\ncachedir=/var/cache/yum\ndebuglevel=1\nlogfile=/var/log/yum.log\nreposdir=/dev/null\nretries=20\nobsoletes=1\ngpgcheck=0\nassumeyes=1\nkeepcache=1\ninstall_weak_deps=0\nstrict=1\n\n# repos\n\n[build]\nname=build\nbaseurl=http://10.30.38.131/kojifiles/repos/kongzi-mips-build/latest/mips64el\nmodule_hotfixes=1\npriority=100\n[build1]\nname=build1\nbaseurl=file:///home/mhl/repo/\nmodule_hotfixes=1\npriority=1\n'
```

### 复制cfg至指定配置目录  
```
cp xxx.cfg /etc/mock/koji/xxx.cfg
```

### 执行初始化(可有可无)
```
mock -r koji/xxx init
```

### 编译某个src包
```
mock -r koji/xxx rebuild xxx.src.rpm
```

### 查看编译日志和编译结果
```
cd /var/lib/mock
```
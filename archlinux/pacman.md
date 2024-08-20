## 用法
#### 安装指定的包
要安装单个或者一系列软件包
`pacman -S 包名_1 包名_2 ...`

要通过正则表达式安装一系列软件包
`'pacman -S $(pacman -Ssq 包正则表达式)'`

有时软件包有多个版本，放在不同的仓库内（例如extra和testing）。在以下示例中，要安装extra仓库的版本，需要在包名称前定义仓库名
`pacman -S extra/包名`

要安装多个含有相似名称的软件包，可以使用花括号扩展
`pacman -S plasma-{desktop,mediacenter,nm}`
可以多层扩展到需要的层次
`pacman -S plasma{workspace{,-wallpapers},pa}`

#### 安装包组
一些包属于一个可以同时安装的软件包组。例如，运行下面的命令
`pacman -S gnome`
会提醒用户选择`gnome`内需要安装的包

有的包组包含大量的软件包，有时用户只需要其中几个。除了逐一键入序号外，pacman还支持选择或排除某个区间内的软件包
`Enter a selection (default=all): 1-10 15`
这将选中序号1到10和15的软件包。而
`Enter a selection (default=all): ^5-8 ^2`
将会选中除了序号5到8和2之外的所有软件包

想要查看那些包属于gnome组，运行
`pacman -Sg gnome`

#### 删除软件包
删除单个软件包，保留其全部已经安装的依赖关系
`pacman -R package_name`

删除指定软件包，及其所有没有被其他已安装软件包使用的依赖关系
`pacman -Rs package_name`
警告：删除类似gnome这样的软件包组时，将会忽略组中软件包的安装原因，因为实际操作上执行的是逐一删除软件组的每一个软件，依赖软件包的安装原因不会被忽略。
上面这条命令在移除包含其他所需包的组时有时候会拒绝运行，这种情况下可以尝试
`pacman -Rsu package_name`

要删除软件包和所有依赖这个软件包的程序
警告：此操作是递归的，请小心检查，可能会一次删除大量的软件包
`pacman -Rsc package_name`

要删除一个被其他软件包依赖的软件包，但是不删除依赖这个软件包的其他软件包
警告：此操作有破坏系统的能力，应该尽量避免使用
`pacman -Rdd package_name`

pacman 删除某些程序时会备份重要配置文件，在其后面加上*.pacsave扩展名。-n选项可以避免备份这些文件
`pacman -Rn package_name`

#### 升级软件包
Arch只支持系统完整升级
一个pacman命令就可以升级整个系统
`pacman -Syu`

#### 查询数据库
pacman使用`-Q`参数查询本地软件包数据库，`-S` 查询同步数据库，以及`-F`查询文件数据库。

pacman可以在包数据库中查询软件包，查询位置包含了软件包的名字和描述
`pacman -Ss string1 string2 ...`
有时，`-s`的内置正则会匹配很多不需要的结果，所以应当指定仅搜索包名，而非描述或其他字段
`pacman -Ss '^vim-'`

要查询已安装的软件包
`pacman -Qs string1 string2 ...`

按文件名查找软件库
`pacman -F string1 string2 ...`

显示软件包的详尽信息
`pacman -Si package_name`

查询本地安装包的详细信息
`pacman -Qi package_name`
使用两个`-i`将同时显示备份文件和修改状态
`pacmam -Qii package_name`

要获取已安装软件包所包含文件的列表
`pacman -Ql package_name`

查询远程库中软件包包含的文件
`pacman -Fl package_name`

检查软件包安装的文件是否都存在
`pacman -Qk package_name`
两个参数`k`将会执行一次更彻底的检查。

查询数据库获取某个文件属于那个软件包
`pacman -Qo /path/to/file_name`

查询文件属于远程数据库中的那个软件包
`pacman -F /path/to/file_name`

要罗列所有不再作为依赖的软件包
`pacman -Qdt`

要罗列所有明确安装而且不被其他包依赖的软件包
`pacman -Qet`

#### 其他命令
升级系统时安装其他软件包
`pacman -Syu package_name1 package_name2 ...`

下载包而不安装它
`pacman -Sw package_name`

安装一个本地包
`pacman -U /path/to/package_name-version.pkg.tar.zst`

要将本地包保存至缓存，可执行
`pacman -U file:///path/to/package_name-version.pkg.tar.zst`

安装一个远程包（不再pacman配置的源里面）
`pacman -U http://www.example.com/repo/example.pkg.tar.zst`

#### 安装原因
- 显式安装：那些真正地被传递给通用pacman`-S`和`-U`命令的包
- 依赖：那些虽然从未被传递给pacman安装命令，但由于被其他显式安装的包需要从而被隐式安装的包
当安装软件包时，可以把安装原因强制设为依赖
`pacman -S --asdeps package_name`
显式安装的软件包列表可用`pacman -Qe`获取，设置为依赖的软件包可用`pacman -Qd`获取
改变某个已安装软件包的安装原因，可以执行
`pacman -D --asdeps package_name`
反过来的对应参数`--asexplicit`

#### 查询一个包含具体文件的包名
同步文件数据库
`pacman -Fy`
查询包含某个文件的包名
`pacman -F pacman`

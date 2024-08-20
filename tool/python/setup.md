## setup 常用参数

|参数|说明|
|:-|:-|
|name|包名称|
|version|包版本|
|author|程序的作者|
|author_email|程序作者的邮箱|
|maintainer|维护者|
|maintainer_email|维护者的邮箱|
|url|程序的官网地址|
|license|程序的授权信息|
|description|程序的简单描述|
|long_description|程序的详细描述|
|platforms|程序使用的软件平台列表|
|classifiers|程序得到所属分类列表|
|keywords|程序的关键字列表|
|packages|需要处理的包目录|
|py_modules|需要打包的python单文件列表|
|download_url|程序的下载地址|
|cmdclass|添加自定义命令|
|package_data|指定包内需要包含的数据文件|
|include_package_data|自动包含包内所有受版本控制的数据文件|
|exclude_package_data|当include_package_data为True时该选项用于排除部分文件|
|data_files|打包时需要打包的数据文件，如图片、配置文件等|
|ext_modules|指定扩展模块|
|scripts|指定可执行脚本，安装时脚本会被安装到系统PATH路径下|
|package_dir|指定那些目录下的文件被映射到那个源码包|
|requires|指定依赖的其他包|
|provides|指定可以为那些模块提供依赖|
|install_requires|安装是需要安装的依赖包|
|entry_points|动态发现服务和插件|
|setup_requires|指定运行setup.py文件本身所依赖的包|
|dependency_links|指定依赖包的下载地址|
|extras_requires|当前包的高级/额外特性需要依赖的分发包|
|zip_safe|不压缩包，而是以目录的形式安装|

[更多参见](https://setuptools.pypa.io/en/latest/setuptools.html)
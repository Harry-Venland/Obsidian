mvn命令

概述

mvn是一个开发人员比较常用的一个项目管理工具，主要是对项目的创建、编译、打包操作。

mvn需要jdk的支持，本次使用的maven版本为3.8.3

配置maven
下载maven，并解压,把解压后的maven放在`/usr/share/`中
```shell
tar -zxvf maven-3.8.3.tar.gz -C /usr/share
```
打开`/etc/profile`文件,配置完成后，执行`source /etc/profile`

```shell
export MAVEN_HOME=/usr/share/apache-maven-3.8.3
export PATH=$MAVEN_HOME/bin:$PATH
```

mvn命令参数

```shell
mvn -v, --version 显示版本信息;
mvn -V, --show-version 显示版本信息后继续执行Maven其他目标;
mvn -h, --help 显示帮助信息;
mvn -e, --errors 控制Maven的日志级别,产生执行错误相关消息;
mvn -X, --debug 控制Maven的日志级别,产生执行调试信息;
mvn -q, --quiet 控制Maven的日志级别,仅仅显示错误;
mvn -Pxxx 激活 id 为 xxx的profile (如有多个，用逗号隔开);
mvn -Dxxx=yyy 指定java全局属性;
mvn -o , --offline 运行offline模式,不联网更新依赖;
mvn -N, --non-recursive 仅在当前项目模块执行命令,不构建子模块;
mvn -pl, --module_name 在指定模块上执行命令;
mvn -ff, --fail-fast 遇到构建失败就直接退出;
mvn -fn, --fail-never 无论项目结果如何,构建从不失败;
mvn -fae, --fail-at-end 仅影响构建结果,允许不受影响的构建继续;
mvn -C, --strict-checksums 如果校验码不匹配的话,构建失败;
mvn -c, --lax-checksums 如果校验码不匹配的话,产生告警;
mvn -U 强制更新snapshot类型的插件或依赖库(否则maven一天只会更新一次snapshot依赖);
mvn -npu, --no-plugin-updates 对任何相关的注册插件,不进行最新检查(使用该选项使Maven表现出稳定行为，该稳定行为基于本地仓库当前可用的所有插件版本);
mvn -cpu, --check-plugin-updates 对任何相关的注册插件,强制进行最新检查(即使项目POM里明确规定了Maven插件版本,还是会强制更新);
mvn -up, --update-plugins [mvn -cpu]的同义词;
mvn -B, --batch-mode 在非交互（批处理）模式下运行(该模式下,当Mven需要输入时,它不会停下来接受用户的输入,而是使用合理的默认值);
mvn -f, --file <file> 强制使用备用的POM文件;
mvn -s, --settings <arg> 用户配置文件的备用路径;
mvn -gs, --global-settings <file> 全局配置文件的备用路径;
mvn -emp, --encrypt-master-password <password> 加密主安全密码,存储到Maven settings文件里;
mvn -ep, --encrypt-password <password> 加密服务器密码,存储到Maven settings文件里;
mvn -npr, --no-plugin-registry 对插件版本不使用~/.m2/plugin-registry.xml(插件注册表)里的配置;
```

mvn编译命令
```shell
mvn clean 清空生成的文件
mvn clean install 删除在编译
mvn test-compile 编译并测试
mvn package 打包生成jar/war
mvn -Dtest package 只打包不测试
mvn jar:jar 只打jar包
mvn clean install -DskipTest 打包项目到本地仓库
mvn clean install -Dmaven.test.skip=ture 给任何目标添加maven.test.skip属性跳过单元测试
```


参考链接：
        https://www.cnblogs.com/javastack/p/12982472.html
        https://www.runoob.com/maven/maven-tutorial.html
        https://docs.fedoraproject.org/en-US/java-packaging-howto/manpage_pom_xpath_inject/

# linux图形化登录过程

## 涉及的软件包
- systemd
- Xorg/Wayland
- Display Manager
- Desktop Environment
- PAM

## 启动的主要进程和进程间的交互方式
- systemd: 作为初始化进程，负责启动和管理其他进程
- 

1. 涉及的主要软件包:
 - systemd: Deepin 使用 systemd 作为初始化和服务管理器
 - lightdm: Deepin 默认使用 LightDM 作为显示管理器
 - deepin-session: Deepin 桌面环境的会话管理器
 - dde-daemon: Deepin 桌面环境的后台服务管理器
 - startdde: Deepin 桌面环境的启动脚本
 - dde-dock: Deepin 的任务栏和启动器
 - dde-launcher: Deepin 的应用程序启动器
 - dde-control-center: Deepin 的控制中心

2. 启动的主要进程和进程间的交互方式:
 - systemd(PID 1)通过 systemd 服务文件启动 lightdm.service
 - lightdm 启动 Xorg 服务器,并显示登录界面
 - 用户输入凭据后,lightdm 调用 PAM 模块进行身份验证
 - 验证通过后,lightdm 启动 deepin-session 进程
 - deepin-session 启动 dde-daemon、startdde 等进程
 - startdde 启动 dde-dock、dde-launcher 等桌面组件
 - 桌面组件通过 D-Bus 与 dde-daemon 等后台服务通信
 - 用户操作通过 X11 协议传递给 Xorg 服务器,再由相应的桌面组件处理

3. 核心进程的主要功能:
 - systemd:
  - 初始化系统并管理服务的启动和停止
  - 提供日志管理和状态监控
 - lightdm:
  - 提供图形化登录界面
  - 处理用户身份验证,并启动桌面会话
 - Xorg 服务器:
  - 初始化和管理显示设备
  - 处理键盘、鼠标等输入事件
 - deepin-session:
  - 启动并管理 Deepin 桌面环境的核心组件
  - 处理会话的启动、停止和恢复
 - dde-daemon:
  - 提供 Deepin 桌面环境的后台服务
  - 包括电源管理、音频管理、网络管理等
 - dde-dock:
  - 提供任务栏和启动器功能
  - 显示打开的应用程序和系统托盘图标
 - dde-launcher:
  - 提供应用程序启动器功能
  - 管理应用程序的分类和搜索
 - dde-control-center:
  - 提供系统设置和个性化配置界面
  - 管理显示、声音、网络、账户等设置

案例学习:
1. 开机后,systemd 启动 lightdm.service,lightdm 显示登录界面
2. 用户输入账号和密码,lightdm 调用 PAM 模块验证身份
3. 验证通过后,lightdm 启动 deepin-session 进程
4. deepin-session 启动 dde-daemon、startdde 等进程
5. startdde 启动 dde-dock、dde-launcher 等桌面组件
6. 用户可以通过 dde-launcher 启动应用程序,打开的应用会显示在 dde-dock 上
7. 用户可以通过 dde-control-center 调整系统设置
8. 当用户注销或关机时,deepin-session 会通知各个组件退出,并停止相关服务
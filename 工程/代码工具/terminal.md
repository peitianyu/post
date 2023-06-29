---
title: 几款terminal
date: 2021-10-19 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - tool
---

### 终端

`Powershell`,`Windows terminal`,`wsl-terminal`,`fluent terminal`,`xshell`,`Mobaxterm`,`putty`

### Powershell

最基础的远程终端,只需要通过ssh指令连接远程终端即可.如,`ssh.exe pty@192.168.30.100`

### Windows terminal

同样可以用指令打开,同时也可以配置,打开wsl,ssh都比较方便,点击即用.但有些丑.当然也可以配置网上教程挺多,再次不做过多介绍.

#### 安装:

```bash
# powershell输入
winget install --id=Microsoft.WindowsTerminal -e
# 通过Chocolatey
choco upgrade microsoft-windows-terminal
```

#### 右键打开windows terimnal(未测试)

保存为`install.bat`文件打开

```bash
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\Directory\Background\shell\Windows Terminal]
; 右键菜单中显示的名称
@="Windows Terminal"
; 右键菜单中显示的图标
"Icon"="C:\\Toolkits\\Icons\\App\\WindowsTerminal.ico"
; 仅支持在按住Shift+右键的时候才显示
"Extended"=""
; 
"NoWorkingDirectory"=""
; 在菜单中显示或隐藏
"ShowBasedOnVelocityId"=dword:00639bc8


[HKEY_CLASSES_ROOT\Directory\Background\shell\Windows Terminal\Command]
@="C:\\Users\\xyz\\AppData\\Local\\Microsoft\\WindowsApps\\wt.exe -d \"%V\""
```

### fluent terminal

好看但没有`windows terminal`那么方便,不过可以设置快捷键,也还行.

#### 安装

```shell
choco install fluent-terminal
```

```shell
# 下载zip文件解压安装
https://github.com/felixse/FluentTerminal/releases
```

下载完毕后解压，选择 `Install.ps1` 文件，右键使用 powershell 运行。

#### 右键打开

保存为`install.bat`文件打开

```
reg add "HKCU\Software\Classes\Directory\shell\Open Fluent Terminal here\command" /d "\"%LOCALAPPDATA%\Microsoft\WindowsApps\flute.exe\" new \"%%1\"" /f

reg add "HKCU\Software\Classes\Directory\Background\shell\Open Fluent Terminal here\command" /d "\"%LOCALAPPDATA%\Microsoft\WindowsApps\flute.exe\" new \"%%V\"" /f

reg add "HKCU\Software\Classes\LibraryFolder\Background\shell\Open Fluent Terminal here\command" /d "\"%LOCALAPPDATA%\Microsoft\WindowsApps\flute.exe\" new \"%%V\"" /f
```

#### 右键关闭

保存为`Uninstall.bat`文件打开

```
reg delete "HKCU\Software\Classes\Directory\shell\Open Fluent Terminal here" /f

reg delete "HKCU\Software\Classes\Directory\Background\shell\Open Fluent Terminal here" /f
reg delete "HKCU\Software\Classes\LibraryFolder\Background\shell\Open Fluent Terminal here" /f
```

### wsl-terminal

加u个人使用而言,如果多个wsl,则没那么舒爽.当然也可以像powershell那样`wsl -d Ubuntu`这样切换版本

#### 参考网址:

[使用 wsl-terminal 更好地体验 Win 10 WSL 终端环境](https://blog.csdn.net/ly50247/article/details/80131656?ops_request_misc=%7B%22request%5Fid%22%3A%22163481452216780366511110%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163481452216780366511110&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-2-80131656.pc_search_ecpm_flag&utm_term=open-wsl&spm=1018.2226.3001.4187)

写的比较详细就不具体赘述了

### putty

是一款集合ssh,串口的远程软件,但每次都需要开启后配置参数,对于懒人不友好.

#### 参考网址:

[putty使用教程(总结)](https://blog.csdn.net/weixin_40705360/article/details/104008419?ops_request_misc=%7B%22request%5Fid%22%3A%22163481518116780366579230%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163481518116780366579230&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-104008419.pc_search_ecpm_flag&utm_term=putty使用教程&spm=1018.2226.3001.4187)

### xshell

具有putty相似的功能,同时还有**stfp**文件传输,只需点击即可使用.但对于需要调试gui程序而言,需要购买正式版,嗯............大佬随意.

#### 参考网址:

[Xshell使用基础教程_](https://blog.csdn.net/hxy1625309592/article/details/110356421?ops_request_misc=%7B%22request%5Fid%22%3A%22163481523916780269886657%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163481523916780269886657&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-110356421.pc_search_ecpm_flag&utm_term=xhsell&spm=1018.2226.3001.4187)

### Mobaxterm

集百家之长于一身,因此使用起来没那么方便,但使用后非常舒爽.值得一提的是,远程文件是可以根据终端实时更新的,同时**支持拖拽文件,新建文件,文件夹,本地编辑文件**,这就很爽了.

#### 参考网址:

[全能终端神器mobaxterm入坑指南](https://blog.csdn.net/qq_28721869/article/details/114652520?ops_request_misc=%7B%22request%5Fid%22%3A%22163481551616780269883117%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163481551616780269883117&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-114652520.pc_search_ecpm_flag&utm_term=Mobaxterm&spm=1018.2226.3001.4187)


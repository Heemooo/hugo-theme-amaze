+++
title = "Windows的Linux子系统切换用户"
description = ""
tags = [
    "SubLinux",
    "wsl",
	"Ubuntu"
]
date = "2018-10-11"
categories = [
	"Linux","Windows"
]
+++
# Windows的Linux子系统切换用户
> 最近需要在Windows的Linux子系统中安装一个jdk，用来测试java代码在Linux上的运行情况，但是安装提权时我发现我已经忘了密码了，经过多方查证，找到解决方法，那就是使用Windows的命令将子系统切换到root用户下
```
#在Windows上用管理员权限打开 CMD 或者 PowerShell（win+x，A） 修改默认登录用户为 root ：
lxrun /setdefaultuser root
```
但是我的Linux子系统可能版本比较老，出现下面的错误：
```
警告: lxrun.exe 仅用于配置适用于 Linux 的 Windows 子系统的旧分发版。
```
最后在一篇博客中找到解决办法，使用下面的命令：
```
ubuntu config --default-user  your_username
```
博客地址在这儿[重置Windows中Linux子系统用户密码](https://www.hida.in/reset-the-password-in-linux-bash-in-windows.html)
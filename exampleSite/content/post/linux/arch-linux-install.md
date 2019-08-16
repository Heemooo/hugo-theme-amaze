+++
title = "ArchLinux安装(虚拟机中安装UEFI)"
description = ""
tags = [
    "ArchLinux"
]
date = "2018-09-06"
categories = [
	"Linux"
]
+++
# ArchLinux安装(虚拟机中安装UEFI)
> Arch Linux是一种以轻量简洁为设计理念的Linux发行版，其文档丰富，滚动升级, 新特性支持，无必要的冗余组件

刻录启动盘，建议使用Rufus，（此处为虚拟机，所以跳过此步骤）
1. 设置键盘布局：
```
loadkeys us 
```
2.运行分区工具：
```
cfdisk /dev/sda 
```
查看分区:
```
lsblk
```
#/boot/efi为sda1，/为sda2，/usr为sda3，/home为sda4

3. 安装文件系统

在sda1安装fat文件系统，sda{2,3,4}安装ext4文件系统
```
1     mkfs.vfat /dev/sda1
2     mkfs.ext4 /dev/sda2
4     mkfs.ext4 /dev/sda3
3	mkswap /dev/sda4
4 	swapon /dev/sda4
```
分区情况以实际情况为准
4. 挂载分区
```
mount /dev/sda3 /mnt
mkdir /mnt/{boot,home}
mount /dev/sda1 /mnt/boot
mount /dev/sda2 /mnt/home
```
5. 选择更新源
```
nano /etc/pacman.d/mirrorlist
pacman -Syy
```
建议使用163源

6. 部署基本系统

如果你想使用netstat和ifconfig之类的指令，请加上net-tools。
```
pacstrap -i /mnt base base-devel net-tools
```
7. 生成fstab
 
fstab中记录了挂载的相关信息
```
genfstab -U -p /mnt >> /mnt/etc/fstab
```
8. 基本系统设置
```
arch-chroot /mnt /bin/bash
alias ls='ls --color'
```
1.设置Locale
```
nano /etc/locale.gen
```
这里你至少开启en_US.UTF-8和zh_CN.UTF-8。
```
locale-gen
echo LANG=zh_CN.UTF-8 >> locale.conf
```
这里由于console字体的原因，中文会变成方框，如果你不安装桌面环境，请使用en_US.UTF-8。
2.设置console
```
nano /etc/vconsole.conf
```
输入如下内容
```
KEYMAP=us
FONT=
```
3.设置时区
```
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
这里大家可能会发现BIOS的时间和系统不一样了，我提供一个解决方案。
```
nano /etc/adjtime
```
输入如下内容：
```
0.000000 0 0.000000
0
LOCAL
```
4. 设置主机名
虽说这里的主机名可以有大写，不过我建议大家使用常规的英文小写。
```
echo 主机名 > /etc/hostname
```
你还得修改/etc/hosts文件的内容。
```
nano /etc/hosts
```
你会看到如下内容：
```
#
# /etc/hosts: static lookup table for host names
#
#<ip-address>   <hostname.domain.org>  <hostname>
 127.0.0.1      localhost.localdomain localhost   主机名   
 ::1            localhost.localdomain localhost   主机名
#End of file
```
请把myhostname替换成你所设置的主机名。
5. 生成初始ramdisk环境
```
mkinitcpio -p linux
```
6. 用户设置

首先是设置root的密码：
```
passwd
```
创建一个日常用户，这里添加wheel用户组是为了能够使用sudo提权
```
useradd -m -g users -G wheel -s /bin/bash 用户名 
passwd 用户名
```
安装sudo
为安全期间，我们可以用sudo来使用root权限
```
pacman -S sudo
```
将用户加入sudo组
在/etc/sudoers加入这个：
```
用户名 ALL=(ALL) ALL
```
也可以去掉#%wheel ALL=(ALL) ALL这一行前面的#
7. 安装网络工具

(有线用户可选 WIFI用户必需)安装wifi配置软件
```
pacman -S dialog wpa_supplicant netctl wireless_tools 
```
现在不安装 重启之后你就不能上网
***
8. 安装grub启动器(不推荐)
```
pacman -S grub-efi-x86_64   
```
#UEFI版本本体
```
pacman -S efibootmgr                    #EFI管理器
pacman -S os-prober   
```
#双系统必需管理器(可选)
grub-install --efi-directory=/boot/efi --bootloader-id=grub #安装进EFI分区
grub-mkconfig -o /boot/grub/grub.cfg                        #创建grub配置文件
#检查文本输出内是否有Windows
***
8. bootctl install（推荐使用）
9. 
这会生成一个/boot/loader/entries/arch.conf文件，如果没有自动生成，需要手动新建并编辑它。
```
nano /boot/loader/entries/arch.conf
```
arch.conf文件这里有一行，改为自己的根分区位置。注意linux条目下的是/vmlinuz-linux，不是/vmlinux-linux，需要多多注意。
```
title          Arch Linux
linux          /vmlinuz-linux
initrd         /initramfs-linux.img
options        root=/dev/sda3 rw
```
然后创建loader.conf文件，并添加以下内容。
```
nano /boot/loader/loader.conf
```
添加的内容：
```
default  arch
timeout  5
```
启用有线网服务
如果虚拟机重启没有网的话需要让它开机自启动。
```
systemctl enable dhcpcd.service
```
安装桌面环境,
安装xorg，
所有的桌面环境都基于X11窗口系统。因此首先需要安装X11的开源版本xorg。
``` 
pacman -S xorg
```
安装xfce4桌面环境,推荐使用，更轻量，安装其它桌面方式请自行百度，
安装完成xorg之后,需要安装xfce4桌面环境.还可以安装额外的工具包。
```
pacman -S xfce4 xfce4-goodies
```
安装显示管理器并启用
这里安装sddm显示管理器。
```
pacman -S sddm
systemctl enable sddm.service
```
也可使用lightDM等显示管理器
+++
title = "Windows上配置免安装版MySQL"
description = ""
tags = [
    "Windows"
]
date = "2019-07-03"
categories = [
	"MySQL"
]
+++
# Windows上配置免安装版MySQL

软件版本`MySQL Community Server 5.7.17`

![alt](https://hemm.site//blog/windows-install-mysql/2.png)

解压到本地，最好不要有中文路径

## 1：下载并解压

下载好后解压到本地。

解压后的文件如下所示

![](https://hemm.site//blog/windows-install-mysql/3.png)

## 2：新建my.ini文件

复制my-default.ini文件并粘贴（没有自己创建一个mysql.ini放到mysql根目录），改名为my.ini

然后将里面的文本内容替换为 

```
[mysql]

# 设置mysql客户端默认字符集

default-character-set=utf8

[mysqld]

#设置3306端口

port = 3306

# 设置mysql的安装目录

basedir=D:\DevPath\mysql-5.7.26

# 设置mysql数据库的数据的存放目录

datadir=D:\DevPath\mysql-5.7.26\data

# 允许最大连接数

max_connections=200

# 服务端使用的字符集默认为8比特编码的latin1字符集

character-set-server=utf8

# 创建新表时将使用的默认存储引擎

default-storage-engine=INNODB
```

 `D:\DevPath\mysql-5.7.26`为mysql的目录

## 3：data文件的创建

以管理员的身份运行并打开cmd。

![](https://hemm.site//blog/windows-install-mysql/1.png)

将文件目录切换到D:\DevPath\mysql-5.7.26\bin（该路径取决于你安装的位置）目录下。

![](https://hemm.site//blog/windows-install-mysql/4.png)

输入` mysqld --initialize-insecure --user=mysql `等待一会打开目录你会发现新建一个data文件夹。

![](https://hemm.site//blog/windows-install-mysql/5.png)

## 4：启动mysql

在bin>下继续输入服务安装命令：

`mysqld install MySQL --defaults-file="D:\DevPath\mysql-5.7.26\my.ini"`

注意替换。

![](https://hemm.site//blog/windows-install-mysql/6.png)

如果这里提示：`The service already exists!`

则输入：`mysqld -remove`

然后输入:`mysqld install MySQL --defaults-file="E:\dataBase\mysql-5.7.17-winx64\my.ini"`

这时会显示：`Service successfully installed.`

然后输入启动指令:`net start mysql`

![](https://hemm.site//blog/windows-install-mysql/7.png)

## 5：本地环境配置

> 若不喜欢用控制台连接也可以不配置该项

在系统环境变量中"新建"一个名为"MySQL_HOME"的变量。变量值为mysql目录

![](https://hemm.site//blog/windows-install-mysql/8.png)

编辑现有的环境变量"Path"，在最后增加"%MySQL_HOME%\bin"。

![](https://hemm.site//blog/windows-install-mysql/9.png)

最后输入控制台输入`mysql -u root`就可以进入mysql了

![](https://hemm.site//blog/windows-install-mysql/10.png)

## 6：设置root密码

控制台输入`mysqladmin -u root -p password  123456`

![](https://hemm.site//blog/windows-install-mysql/11.png)

`输入该命令时会提示让你输入密码，第一次安装没有密码直接按enter键就可以完成设置，以后修改密码时则需要输入`
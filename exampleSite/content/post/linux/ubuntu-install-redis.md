+++
title = "Ubuntu安装redis"
description = ""
tags = [
    "Ubuntu",
    "Redis"
]
date = "2018-09-06"
categories = [
	"Linux"
]
+++
# Ubuntu安装redis
>这里系统ubuntu14.04,redis-3.2.9.tar.gz,用户使用root
## 下载redis(法一)
```
   wget http://download.redis.io/releases/redis-3.2.9.tar.gz
   //如果是云服务器,建议复制文件地址,然后用[wget 地址]下载,这样比本地上传到云服务器要快很多
```
## 解压redis
```
tar -xvzf redis-3.2.9.tar.gz
mv redis-3.2.9 redis //重命名为redis(可有可无)
```
## 编译安装
```
cd redis
make
```

![image](http://note.youdao.com/yws/api/personal/file/E82AD6206D324E188A364BEF7115871F?method=download&shareKey=6df393b7e29aede99dbd177a8b4c6f2c)

## 启动redis
```
src/./redis-server redis.conf
ps -aux|grep redis //查看redis的进程
netstat -nlt|grep 6379 //在终端中通过启动命令检查Redis服务器状态
/etc/init.d/redis-server status //通过启动命令检查Redis服务器状态
```
## 测试
```
src/./redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```
## 命令安装(法二)
```
$sudo apt-get update
$sudo apt-get install redis-server
```
## 启动 Redis
```
/etc/init.d/redis-server start
// stop restart
```
## 查看 redis 是否启动？
```
ps -aux|grep redis //查看redis的进程
netstat -nlt|grep 6379 //在终端中通过启动命令检查Redis服务器状态
/etc/init.d/redis-server status //通过启动命令检查Redis服务器状态
```
## 打开redis终端
```
redis-cli
```
127.0.0.1 是本机 IP ，6379 是 redis
服务端口。现在我们输入 PING 命令。
```
redis 127.0.0.1:6379> ping
PONG
```
以上说明我们已经成功安装了redis。
# 配置redis
## 使用Redis的访问账号

默认情况下，访问Redis服务器是不需要密码的，为了增加安全性我们需要设置Redis服务器的访问密码。设置访问密码为redis。
```
vim /etc/redis/redis.conf
#取消注释requirepass
requirepass redis
```
## 让Redis服务器被远程访问 
默认情况下，Redis服务器不允许远程访问，只允许本机访问，所以我们需要设置打开远程访问的功能。
```
#注释bind
#bind 127.0.0.1
#加上
bind 0.0.0.0
```
修改后，重启Redis服务器。
```
sudo /etc/init.d/redis-server restart
```
未使用密码登陆Redis服务器
```
redis-cli

redis 127.0.0.1:6379> keys *
(error) ERR operation not permitted
```
发现可以登陆，但无法执行命令了。

登陆Redis服务器，输入密码
```
redis-cli -a redis

redis 127.0.0.1:6379> keys *
1) "key2"
2) "key3"
3) "key4"
```
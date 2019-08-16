+++
title = "Ubuntu开启定时任务"
description = ""
tags = [
    "Ubuntu",
    "corn"
]
date = "2018-10-05"
categories = [
	"Linux"
]
+++
# Ubuntu开启定时任务
> 最近线上项目出现了一个问题，jvm出现内存溢出导致项目不能访问，但是重启之后就正常了，这个问题在本地还没有重现，因此想了一个不是办法的办法，就是每天重启一次项目，因此涉及到ubuntu设置定时任务。

### 首先开启定时任务日志

```
1.修改rsyslog文件，将/etc/rsyslog.d/50-default.conf 文件中的#cron.*前的#删掉；
2.重启rsyslog服务service rsyslog restart
3.重启cron服务service cron restart
查看日志：

more /var/log/cron.log
```
### 设置定时任务
```
crontab -e
0    12   *   *   *  echo "test" >> test.log
#分  时   日   月  周  |《==============命令行=======================》|
cat /var/log/cron.log 查看日志
```
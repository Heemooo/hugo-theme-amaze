+++
title = "常用SQL语法记录"
description = ""
tags = [
    "Sql"
]
date = "2019-05-27"
categories = [
	"MySQL"
]
+++
# 常用SQL语法记录

> 记录在开发中常遇到的SQL使用方法，以备后续查找

1.创建存储过程

```sql
DROP TEMPORARY TABLE IF EXISTS `#data_tempa`;
create TEMPORARY TABLE IF NOT EXISTS `#data_tempa` select *from a;
```

2.按固定顺序排序

```sql
ORDER BY FIELD(field_name,value3,value2,value4,value1)
```
![alt](http://cdn.hemm.site/zhangzifengW020160329353623395849.jpg)
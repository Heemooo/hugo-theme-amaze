+++
title = "利用Java反射封装Jdbc的查询结果"
description = ""
tags = [
    "Reflect"
]
date = "2018-10-19"
categories = [
	"Java"
]
+++
# 利用Java反射封装Jdbc的查询结果
> 开发中若是使用Jdbc来查询数据，结果会保存在一个ResultSet结果集中，但是我们希望得到的是一个实体Bean，因此每次需要我们手动解析ResultSet，将结果集的数据设值到Bean中，这样每次查询操作时都需要解析一遍，尽管可以提取成公共的方法，但是每个查询的DAO中任然需要手动解析，且不够灵活。因此想到使用反射来解析ResultSet自动封装结果集到实体Bean之中。
## 代码实现
一般来说，数据库字段是以下划线分割命名，而实体类中使用驼峰命名，因此需要有个东西来标识对应关系（也可以不用，查询的时候使用as命名字段跟实体类中相同就行），这里使用注解来标识
```
@Target(ElementType.FIELD)//目标位置
@Retention(RetentionPolicy.RUNTIME)//生效时间
public @interface Column {
    /**
     * 记录数据库表中的字段名称
     */
    String value();
}
```
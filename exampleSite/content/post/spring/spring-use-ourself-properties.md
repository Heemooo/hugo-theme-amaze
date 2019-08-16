+++
title = "SpringBoot自定义配置文件"
description = ""
tags = [
    "SpringBoot",
    "Spring"
]
date = "2019-07-04"
categories = [
	"Java"
]
+++
# SpringBoot自定义配置文件

> 最近打算将blog项目重构，由原来的blade框架搭建的tale博客重构为SpringBoot+Freemarker+MyBatis+mysql的项目，这次重构会尽可能的用上SpringBoot的中的组件，方便我们熟悉SpringBoot
>
> [项目地址](<https://github.com/Heemooo/blog>)

## 第一步，创建配置文件

在resources下创建blog.properties，内容如下

```
blog.enableCdn=true
blog.version=0.1
```

在config包下创建BlogProperties

```java
@Configuration
@ConfigurationProperties(prefix = "blog")
@PropertySource("classpath:blog.properties")
public class BlogProperties {
    @Value("${blog.enableCdn}")
    public boolean enableCdn;
    @Value("${blog.version}")
    public String version;
}
```

在需要使用的地方注入BlogProperties就可以使用项目配置了
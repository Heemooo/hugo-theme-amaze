+++
title = "SpringBoot项目启动时执行自定义操作"
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
# SpringBoot项目启动时执行自定义操作

> 最近打算将blog项目重构，由原来的blade框架搭建的tale博客重构为SpringBoot+Freemarker+MyBatis+mysql的项目，这次重构会尽可能的用上SpringBoot的中的组件，方便我们熟悉SpringBoot
>
> [项目地址](<https://github.com/Heemooo/blog>)

## 第一步 创建InitListener

在listener包下创建InitListener，实现`ServletContextListener`，并使用`@WebListener`	

```java
@WebListener
public class InitListener implements ServletContextListener {

    @Autowired
    public BlogProperties blogProperties;

    @Override
    public void contextInitialized(ServletContextEvent contextEvent) {
        ServletContext servletContext = contextEvent.getServletContext();
        contextEvent.getServletContext().setAttribute("BlogUtils", FreemarkerUtils.useStaticPacker(BlogUtils.class.getName()));
        servletContext.setAttribute("enableCdn", blogProperties.enableCdn);
        servletContext.setAttribute("version",blogProperties.version);
    }
}
```

## 第二步 启用Listener

在Application类中使用`@ServletComponentScan`注解启用Listener
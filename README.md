## 说明
本主题适用于Hugo 0.57版本及以上，[下载地址](https://github.com/gohugoio/hugo)，Hugo安装请自行查询官方文档或网上教程
## 版权
主题移植自Typecho博客系统的Amaze模板
地址: https://github.com/spiritree/typecho-theme-amaze


## 关于 Amaze 主题
这是一个简洁风格的主题，参考了AmazeUI和[Typecho-Theme-Meizi](https://github.com/tlerbao/Typecho-Theme-Meizi)还有Jekyll的主题Project-pages和[小专栏](https://xiaozhuanlan.com/)，可以说是AmazeUI、Project-pages、小专栏混搭风格。


## 主题亮点
- 基于AmazeUI v3.0.0 alpha.beta框架
- 支持设置顶栏背景图片自定义
- 支持个人社交按钮自定义
- 导航栏、关于我们页面轻松自定义
- 简洁


## 主题框架
- JQuery
- AmazeUI
- Highlight.js


## 主题预览
![page1](https://github.com/Heemooo/amaze/blob/master/images/page1.png)
![page2](https://github.com/Heemooo/amaze/blob/master/images/page2.png)
![page3](https://github.com/Heemooo/amaze/blob/master/images/page3.png)


## 安装
1.在D:盘创建Hugo目录，并在Hugo下创建bin目录和Sites目录
2.将Hugo.exe复制到bin目录下
3.打开cmd,输入：
```
d:
cd Hugo/Sites
set PATH=%PATH%;D:\Hugo\bin
hugo new site heemooo
```
1.克隆主题
```
git clone git@github.com:Heemooo/hugo-theme-amaze.git
```
2.将主题放入Hugo的themes目录下，并重命名为hugo-theme-amaze

3.将主题下的`exampleSite`文件夹的内容复制到heemooo的根目录下

4.cmd进入heemooo目录，执行以下命令即可启动成功
```
cd heemooo
hugo server
```

[LICENSE](https://github.com/Heemooo/amaze/blob/master/LICENSE)
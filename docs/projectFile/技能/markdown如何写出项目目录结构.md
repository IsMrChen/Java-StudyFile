### 前言

使用markdown文档时，想对整个目录做一个说明，看有大佬是这样显示的，比如SpringBoot的代码结构：

```ascii
springboot-hello
├── pom.xml
├── src
│   └── main
│       ├── java
│       └── resources
│           ├── application.yml
│           ├── logback-spring.xml
│           ├── static
│           └── templates
└── target
```

我一直在想这个是咋实现的呢？

然后在这篇文章找到了答案[参考文章地址](https://www.jianshu.com/p/e38a07f824a2)

文章提到使用的是一个叫treer工具



### 安装命令

这里要用到npm的安装命令，那么首先就需要安装node.js，可以参考我的博客

[教程参考](https://blog.csdn.net/Dan1374219106/article/details/108500324)

```
npm install treer -g
```

查看版本：

```
treer --version
```

![treer1](img\treer1.png)

### 使用命令

```
treer //查看目录树
treer -d <指定路径>//查看指定路径的目录树
treer -e <导出路径>//导出当前目录的目录树到特定路径下文件
treer -i "/^regex$/"//忽略目录或文件
```


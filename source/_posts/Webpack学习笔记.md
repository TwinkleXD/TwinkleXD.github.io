---
title: Webpack学习笔记
category:
  - [Webpack]
mathjax: true
toc: true
date: 2023-02-21 10:12:07
tags: 
  - Webpack
  - note
description: Webpack5.0的学习记录
permalink:
sticky:
---

>本质上，webpack 是一个用于现代 JavaScript 应用程序的 <b>静态模块打包工具</b>。

参考：
- [官方文档](https://webpack.docschina.org/concepts/)
- [Webapck5核心打包原理全流程解析](https://juejin.cn/post/7031546400034947108)

# 打包是什么？为什么需要打包？
直白来说，就是将多个js文件或者css文件压缩整合成一个大的整体，使源码打包成能运行在目标环境中的代码。早期浏览器不支持模块化，模块语法要在node环境下才有，多个js文件需要逐一以标签形式引入，不能使用import、export、require等语法。因此通过打包将js模块合并压缩成一个（或者两个），使前端能够进行模块化开发。打包的另一个作用是编译，将一些浏览器不能识别的语法编译成可识别的。

# Webpack的核心作用
1. 有效处理模块化
2. 编译
3. 文件有加密压缩效果，上线安全

# Webpack打包流程
整体分为5个步骤

```mermaid
graph LR
  初始化参数 --> 编译
  编译 --> 模块编译
  模块编译 --> 完成编译
  完成编译 --> 输出文件
```

首先从`webpack.config.js`中读取到对应的配置参数，与`shell`命令中传入的参数合并，得到最终打包的配置参数。然后会调用`webpack()`方法返回一个 compiler<i>(编译器)</i> 方法，创建`compiler`对象，同时注册`Webpack Plugin`。找到配置入口的`entry`代码，调用`compiler.run()`方法进行编译。从入口模块开始分析，调用匹配文件的`loaders`进行文件处理，分析模块与依赖模块，递归进行编译。递归完成后得到模块间的相互依赖关系。整理后将文件输出到`output`对应的文件下。

# 使用方法
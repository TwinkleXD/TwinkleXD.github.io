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
- [官方文档](https://webpack.docschina.org/concepts/)

# 打包是什么？为什么需要打包？
直白来说，就是将多个js文件或者css文件压缩整合成一个大的整体，使源码打包成能运行在目标环境中的代码。早期浏览器不支持模块化，模块语法要在node环境下才有，多个js文件需要逐一以标签形式引入，不能使用import、export、require等语法。因此通过打包将js模块合并压缩成一个（或者两个），使前端能够进行模块化开发。打包的另一个作用是编译，将一些浏览器不能识别的语法编译成可识别的。

# Webpack的核心作用
1. 有效处理模块化
2. 编译
3. 文件有加密压缩效果，上线安全

# 打包原理

# 使用方法
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
description: Webpack的学习记录
permalink:
sticky:
comments: true
---

>本质上，webpack 是一个用于现代 JavaScript 应用程序的 <b>静态模块打包工具</b>。

参考：
- [官方文档](https://webpack.docschina.org/concepts/)
- [Webapck5核心打包原理全流程解析](https://juejin.cn/post/7031546400034947108)

# 打包是什么？为什么需要打包？
就是将多个js、css等前端开发的文件压缩整合成一个大的整体，使源码打包成能运行在目标环境中的代码。
早期浏览器不支持模块化，模块语法要在node环境下才有，多个js文件需要逐一以标签形式引入，不能使用import、export、require等语法。因此通过打包将js模块合并压缩成一个，使前端能够进行模块化开发。打包的另一个作用是编译，将一些浏览器不能识别的语法编译成可识别的。

# Webpack的主要作用
1. 有效处理模块化
2. 编译
3. 文件有加密压缩效果，上线安全

# Webpack核心概念
- Entry：入口文件，webpack开始打包编译时，会从entry指定的文件开始。
- Module：模块，webpack里一切皆模块，一个模块对应一个文件，webpack会从entry开始递归找到所有依赖模块。
- Plugin：插件，本质上是发布订阅模式进行事件监听，在特定时机做对应的事。
- Loader：转换，用于转换非js/json文件为有效模块，能够添加到依赖关系中。
- Output：出口，打包完成输出文件的路径、名称等。

# Webpack打包流程
整体分为5个步骤

```mermaid
graph LR
  初始化参数 --> 编译
  编译 --> 模块编译
  模块编译 --> 完成编译
  完成编译 --> 输出文件
```

首先从`webpack.config.js`中读取到对应的配置参数，与`shell`命令中传入的参数合并，得到最终打包的配置参数。
然后会调用`webpack()`方法返回一个`compiler`方法，创建`compiler`对象，同时注册`Webpack Plugin`。
找到配置入口的`entry`代码，调用`compiler.run()`方法进行编译。从入口模块开始分析，调用匹配文件的`loaders`进行文件处理，分析模块与依赖模块，递归进行编译。
递归完成后得到模块间的相互依赖关系。整理后将文件输出到`output`对应的文件下。

# webpack的性能优化
## 首屏加载优化
### 代码分离
将首屏以外的代码分离出来
### 图片压缩
使用`image-webpack-loader`在打包过程中压缩图片大小
<b>使用方法</b>
- webpack.base.confi.js
```js
module:{
  rules: [{
    test: /\.(pngljpe?glgiflsvg)(\?.*)?$/,
    use:[
      {
        loader:'file-loader',
        options:
        name: '[name].[hash:7].[ext]',
        outputPath:'mobile/img
      },
      {
        loader:'image-webpack-loader
        options: {
          mozjpeg: {
            progressive: true,
            quality: 50
          },
          optipng: {
            enabled: false
          },
          pngquant: {
            quality: [0.5, 0.65],
            speed: 4
          },
          gifsicle: {
            interlaced: false
          }
        }
      }
    ]
  }]
}
```
- vue.config.js
```js
module.exports = {
  ···
  chainWebpack(config){
    config.module
    .rule('images')
    .use('image-webpack-loader')
    .loader('image-webpack-loader')
    .options({
      bypassOnDebug: true
    })
    .end()
  }
}
```

### 组件按需导入
比如[Element](https://element-plus.gitee.io/zh-CN/guide/quickstart.html#按需导入)、[Vant](https://vant-contrib.gitee.io/vant/#/zh-CN/quickstart#fang-fa-er.-an-xu-yin-ru-zu-jian-yang-shi)、[Echarts](https://echarts.apache.org/handbook/zh/basics/import)等第三方库的按需导入方式

### 路由懒加载
当打开页面时才去加载对应文件，利用Vue的异步组件和webpack的代码分割`import()`实现懒加载。
但当路由过多时，需要合理地用webpack的魔法注释对路由进行分组，太多的chunk会影响构建时的速度。

## 如何提高Webpack构建速度
- 多入口情况下，使用`CommonsChunkPlugin`来提取公共代码；
- 通过`externals`配置来提取常用库；
- 利用`DllPlugin`和`DllReferencePlugin`预编译资源模块通过`DllPlugin`来对那些我们引用但是绝对不会修改的npm包来进行预编译，再通过`DllReferencePlugin`将预编译的模块加载进来；
- 使用`Happypack`实现多线程加速编译；
- 使用`webpack-uglify-paralle`来提升`uglifyPlugin`的压缩速度；
- 原理上`webpack-uglify-parallel`采用了多核并行压缩来提升压缩速度；
- 使用`Tree-shaking`和`Scope Hoisting`来剔除多余代码。
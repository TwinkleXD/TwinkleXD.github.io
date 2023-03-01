---
title: this和箭头函数
category:
  - [JavaScript]
mathjax: true
toc: true
date: 2023-03-01 13:57:40
tags:
  - JavaScript
  - note
description: this的指向问题
permalink:
sticky:
---
参考：
[JavaScript的this原理](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)
[es6中箭头函数this的指向](https://www.jianshu.com/p/216f47b54e82)
[es6入门——阮一峰](https://es6.ruanyifeng.com/#docs/function#%E4%BD%BF%E7%94%A8%E6%B3%A8%E6%84%8F%E7%82%B9)

# this的出现
JS允许在函数体内部引用当前环境的其他变量，但函数能在不同环境运行，所以需要在函数内部获取当前的运行环境——this。
# this示例
```js
var x = 1;
const f = function () {
  console.log(this.x)
};

const obj = {
  f: f,
  x: 2,
};

f(); // 1
obj.f(); // 2
```
# this指向
普通函数中，在默认情况（非严格模式）下，没有直接的挂载者（或称调用者）的函数中this是指向window；而在严格模式（use strict）下，没有调用者的this是undefined；使用call，apply，bind (ES5新增) 绑定的，this指的是 绑定的对象。
即：指定调用者为this指向，不指定的this即在window上，不继承.
默认情况下:
```js
window.val = 1;
 var obj = {
   val: 2,
   f: function () {
     this.val *= 2;
     val *= 2;
     console.log(val);
     console.log(this.val);
   }
 };

 obj.f(); // 2 4
 var func = obj.f;
 func(); // 8 8

/* val 始终指向window的val值；obj.f()中函数f实际环境在调用者obj内部，this.val指向obj的属性val；函数func()指向obj内部函数属性的地址，函数本身实际所在环境在obj外部，val和this.val都指向window.val，即window.val进行了两次翻倍。同时func函数调用受obj.f结果影响，func()执行时值为2。 */
```
# 使用所在环境的this引用
  1. 提前保存this：that = this or _this = this
  2. bind回调绑定：function(){}.bind(this)
# 箭头函数（=>）
es6新增，比正常函数简洁，简化回调，可以与变量解构结合。
使用注意：
  - 箭头函数里的this：继承性，指向定义函数所在的对象
  - 不能作为构造函数，即不能使用new命令，否则报错
  - 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替
  - 不可以使用yield命令，即不能用作 Generator 函数
# 箭头函数的this示例
```js
var obj = {
f: function () {
console.log(1,this); // 1 {f: f}
  var f1 = function () {
    console.log(2,this); // 2 window, f1调用时,没有宿主对象,默认是window
  };
  setTimeout(() => {
      console.log(3,this); // 3 {f: f} , 继承1
    })
  f1();
  }
}
obj.f();
var obj = {
f: function () {
console.log(1,this); // 1 {f: f}
  var f1 = function () {
    console.log(2,this); // 2 window, f1调用时,没有宿主对象,默认是window
    setTimeout(() => {
      console.log(3,this); // 3 window, 继承2
    })
  };
  f1();
  }
}
obj.f();

// 以下情况第一个this同样指向window
var func = obj.f;
func();
```

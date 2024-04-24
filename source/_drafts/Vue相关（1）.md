---
title: Vue相关（1）
category:
  - [Vue]
mathjax: true
toc: true
date: 2023-03-02 09:04:27
tags:
  - vue
  - note
description: vue.js的两个核心/vue双向绑定的原理/vue生命周期/父子组件通信/页面级组件传值/动态组件
permalink:
sticky:
comments: true
---
# vue.js的两个核心
数据驱动和组件化
## 数据驱动
当数据发生变化的时候，用户页面会发生相应的变化，不需要手动去修改DOM。
## 组件化
组件将页面划分成独立的、可重复用的部分，可以对每个部分进行单独的操作。
# vue双向绑定原理

# vue生命周期
Vue实例从创建到销毁的过程，也就是从开始创建、初始化数据、编译模板、挂载Dom→渲染、更新→渲染、卸载等一系列过程。在这个过程中，会运行生命周期钩子函数，使一些代码能在特定阶段执行。
## 生命周期钩子函数

### 2.0到3.0的变化
| vue2.0 | vue3.0 |
| ------ | ------ |
| beforeCreate | setup() |
| created | setup() |
| beforeMount | onBeforeMount |
| mounted | onMounted |
| beforeUpdate | onBeforeUpdate |
| updated | onUpdated |

# 父子组件通信
## 父组件向子组件传值
在父组件的setup中，先import引入子组件，然后在子组件的标签上添加要传递的值；
在子组件的setup中，用definedProps的方式接收父组件传来的值
```vue
// FatherComp.vue
<template>
  <div>
    <ChildComp :toChild='state.toChild' />
  </div>
</template>
<script setup>
  import ChildComp from './xxx/ChildComp.vue'
  import { reactive } from 'vue'
  const state = reactive({
    toChild = 'paramToChild'
  })
</script>
```
```vue
// ChildComp.vue
<template>
  <p>{{prop}}</p>
</template>
<script setup>
import { defineProps } from 'vue'
const prop = defineProps({
  type: String
})
<script>
```
## 子组件向父组件传值
最常用的Emit方法

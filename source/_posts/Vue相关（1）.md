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
description: vue.js的两个核心/vue双向绑定的原理/vue生命周期/组件通信/页面级组件传值/动态组件
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
<em>2到3的变化</em>
| vue2 | vue3 |
| ------ | ------ |
| beforeCreate | setup() |
| created | setup() |
| beforeMount | onBeforeMount |
| mounted | onMounted |
| beforeUpdate | onBeforeUpdate |
| updated | onUpdated |

# 组件通信
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
  toChild: {
    type: String
  }
})
<script>
```
## 子组件向父组件传值
最常用的Emit方法
```vue
// FatherComp.vue
<template>
  <div>
    <child-comp @childClick='clickValue' />
  </div>
</template>
<script setup>
  import ChildComp from './xxx/ChildComp.vue'
  const clickValue = (value) => {
    console.log('child-value', value)
  }
</script>
```

```vue
// ChildComp.vue
<template>
  <button @click="handleSubmit" type="button">
    click
  </button>
</template>
<script setup>
import { defineEmits } from 'vue'
const emits = defineEmits(['childClick'])
const handleSubmit = () => {
  emits('childClick', 'childtest')
}
</script>
```

## provide/inject
> prop逐级透传问题：多层级嵌套组件的情况下，props将沿着组件链逐级传递下去，非常麻烦。

父组件作为<b>依赖提供者</b>，他的后代组件树，都可以<b>注入</b>父组件提供给整个链的依赖。
```vue
// FatherComp.vue
<template>
  <div>
    <child-comp />
  </div>
</template>
<script setup>
  import ChildComp from './xxx/ChildComp.vue'
  import { provide } from 'vue'
  provide('toChild', 'paramToChild')
</script>
```
```vue
// ChildComp.vue
<template>
  <p>{{ childprop }}</p>
</template>
<script lang="ts" setup>
import { inject } from 'vue'
const childprop = inject('toChild')
</script>
```

## Refs
### ref属性用法
ref属性可以获取DOM节点，相比起原生js的方法，更加方便。vue2和3用法不同
```vue
// vue2
// 在对应标签上绑定ref属性并且命名xxx，然后直接使用this.$ref.xxx即可
<template>
  <div ref="refTest">试试ref</div>
</template>
<script>
  export default {
    mouted() {
      console.log(this.$ref.refTest)
    }
  }
</script>

```

```vue
// vue3
// vue3的setup中不使用this，自定义同名变量后使用
<template>
  <div ref="refTest">试试ref</div>
</template>

<script setup>
  import { onMounted, ref } from 'vue'
  const refTest = ref(null)
  onMounted(() => {
    console.log(refTest.value)
  })
</script>
```
### vue3传值
```vue
// FatherComp.vue
<template>
  <div>
    <child-comp ref="childDemo" />
    <button @click="getChild">获取子组件参数</button>
  </div>
</template>

<script setup>
  import ChildComp from '../components/ChildComp.vue'
  import { ref } from 'vue'
  const childDemo = ref()
  const getChild = () => {
    console.log('old', childDemo.value.childInfo)
    childDemo.value?.handleVal()
    console.log('new', childDemo.value.childInfo)
  }
</script>
```
```vue
// ChildComp.vue
<template>
  <p>ChildComp</p>
</template>

<script setup>
import { ref } from 'vue'
const childInfo = ref('info')
const handleVal = () => {
  childInfo.value = 'info change'
}

defineExpose({
  childInfo,
  handleVal
})
</script>

```


## Pinia
---
### 1. 安装 Pinia
安装Pinia：  
`npm install pinia`

依赖导入（Vue中的 src-> main.js 里面）：
```java
import { createPinia } from 'pinia' //引入Pinia
app.use(createPinia()) //使用Pinia
```

### 2. 使用 Pinia
在src中创建store文件夹，下面每个文件为一个仓库
例如创建一个counter.js（独立仓库）
```js
import { defineStore } from 'pinia'
import { ref } from 'vue'

export const useCounterStore = defineStore('counter',()=>{
    //声明数据 state：count
    const count = ref(0)

    //声明操作数据的方法 action
    const addCount = ()=> count.value++
    const subCount = ()=> count.value--

    //声明基于数据派生的计算属性 getters（用computed）
    const double = computed(()=> count.value * 2)
    
    //继续声明数据：（组合式开发）
    const msg = ref('hello pinia')

    return{ //返回出去给组件用
        count,
        addCount,
        subCount,
        msg
    }
})
```
组件使用Pinia：
```vue
<script setup>
import { useCounterStore } from '@/store/counter.js' //导入pinia仓库
const counterStore = useCounterStore() //使用仓库
</script>

<template>
    <div>{{counterStore.count}}</div>
    <div>{{counterStore.msg}}</div>
    <div>{{counterStore.double}}</div>
    <div @click="counterStore.addCount">方法+</div>
</template>
```

### 3. Pinia 异步写法
在src中创建store文件夹，下面每个文件为一个仓库
例如创建一个channel.js（独立仓库）
```js
import { defineStore } from 'pinia'
import { ref } from 'vue'
import axios from 'axios'

export const usechannelStore = defineStore('channel',()=>{
    //声明数据 state
    const channelList = ref([])

    //声明操作数据的方法 action
    const getList = async()=>{
        //支持异步
        const { data: {data}} = await axios.get('http://127.0.0.1/')
        channelList.value = data.channels //这里上面的解构data
        console.log(data.channels)
    }

    //声明基于数据派生的计算属性 getters（用computed）


    return{ //返回出去给组件用
        channelList,
        getList
    }
})
```

### 4. Pinia 解构
数据结构会丢失ref响应式，所以数据结构要加上storeToRefs()，方法不用
```vue
<script setup>
import { useCounterStore } from '@/store/counter.js' //导入pinia仓库
const counterStore = useCounterStore() //使用仓库

const { channelList } = storeToRefs(counterStore)
coust { getList } = channelStroe
</script>
```

### 5. Pinia 持久化
能够将数据存在本地，每次刷新都不会清空  
`pnpm add pinia-plugin-persistedstate -D`

依赖导入（Vue中的 src-> main.js 里面）：
```java
import persist from 'pinia-plugin-persistedstate'
app.use(createPinia().use(persist)) //在pinia实例后接着use持久化插件
```

启动持久化：（第三个参数）
```js
import { defineStore } from 'pinia'
import { ref } from 'vue'
import axios from 'axios'

export const usechannelStore = defineStore('channel',()=>{
    //声明数据 state
    const channelList = ref([])

    //声明操作数据的方法 action
    const getList = async()=>{
        //支持异步
        const { data: {data}} = await axios.get('http://127.0.0.1/')
        channelList.value = data.channels //这里上面的解构data
        console.log(data.channels)
    }

    //声明基于数据派生的计算属性 getters（用computed）


    return{ //返回出去给组件用
        channelList,
        getList
    }
},{
    persist: true //开启持久化---------------------------
}
)
```

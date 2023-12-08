## Vue3
---
### 1. 用 pnpm 管理包
pnpm是pnm的升级版（用npm也能创建基于vite的vue3），不过pnpm比pnm的速度快2倍 

---
先要进行全局安装pnpm：  
`npm install -g pnpm`

pnpm创建vue：   
`pnpm create vue`  
创建完成后cd 项目，进行依赖首次安装（下面有命令）

pnpm安装依赖：   
`pnpm install`

pnpm安装包：  
`pnpm add axios -D`

pnpm移除包：  
`pnpm remove axios`

pnpm运行：  
`pnpm dev`

### 2. 组合式setup - ref() 
```vue
将该数据类型或者对象变为响应式，能够实时更新
ref() 和 reative()： //ref包含了reative()，底层还是用reative()实现
------------------------
<script setup>
import {ref} from 'vue'
const count = ref(0) //将0转化为对象，并通过.value取值
const setCount = ()=>{
    count.value++
} 
</script>

<template>
    <div>{{count}}</div> //template中用ref不用.value
    <button @click="setCount">+1</button>
</template>
```

### 3. 组合式setup - computed()
```vue
计算属性，过滤
------------------------
<script setup>
import {computed,ref} from 'vue'
const list = ref([1,2,3,4,5,6,7,8]) 
const computedList = computed(()=>{
    return list.value.filter(item => itme>2) //将大于2的输出
})
const addfn = ()=>{
    list.value.push(666)
}
</script>

<template>
    <div>原始：{{list}}</div> 
    <div>计算后的{{computedList}}</div> 
    <button @click="addFn">+666</button>
</template>
```

### 4. 组合式setup - watch()
```vue
可以监视值的变化，做出相应的处理
immediate 立即执行（页面开始执行）
deep 深度监视（watch默认是浅层）
------------------------
<script setup>
import {watch,ref} from 'vue'
const count = ref(0)
const nickname = '张三'
count changeCount = ()=>{ //定义修改count的方法，用于被watch监听
    count.value++
}
count changenickname = ()=>{ //定义修改nickname的方法，用于被watch监听
    nickname.value = '李四'
}
//------监听：
//监听单个值的变化
watch(count, (newValue,oldValue)=>{ 
    console.log(newValue,oldValue)
})
//监听多个值的变化
watch([count,naickname], (newArr,oldArr)=>{ 
    console.log(newArr,oldArr)
})
//immediate 立即执行
watch(count, (newValue,oldValue)=>{ 
    console.log(newValue,oldValue)
},{
    immediate: true 
})
------------
//deep 深度监听（当监听复杂对象时）
const userInfo = ref({
    name: 'zs',
    age: 18
})
const setUserInfo = ()=>{
    userInfo.value.age++
}
//深度监视 - 当复杂对象有个属性变化时，就能监听到
watch(userInfo,(newValue)=>{
    console.log(newValue)
},{
    deep: true
})
//深度监视 - 精准监视（只监听某个值）
watch(()=> userInfo.value.age,(newValue,oldValue)=>{
    console.log(newValue,oldValue)
})

</script>

<template>
</template>
```

### 5. 组合式setup - 生命周期
```vue
setup 其实是beforeCreate/create 的组合，vue3中的script都是进入页面就调用
```

### 6. 组合式setup - 父子通信
```vue
父传子：
1.在子组件绑定一个事件
2.子页面用props接受defineProps中的方法
//父页面：
<script setup>
const money = ref(100)
const getMoney = ()=>{
    money.value+=10
}
</script>

<template>
    <button @click="getMoney">挣钱</button>
    <SonCom :money="money"></SonCom> //在子组件绑定一个事件
</template>
//子页面：
<script setup>
const props = defineProps({ //子页面用props接受defineProps中的方法
    money:Number
})
console.log(props.money)
</script>

<template>
    <div class="son">{{money}}</div>
</template>
----------------------------------------
子传父：
1.子组件定义一个方法用于监听子页面的数据
2.子页面用emit传递defineEmits中的方法
//父页面
<script setup>
const money = ref(100)
const changeFn = (newMoney)=>{ 
    money.value = newMoney
}
</script>

<template>
    <SonCom @changeMoney="changeFn"></SonCom> 
</template>
//子页面：
<script setup>
const emit = defineEmits(['changeMoney'])
const buy = ()=>{
    emit('changeMoney',s)
}
</script>

<template>
    <button @click="buy">花钱</button>
</template>
```

### 7. 组合式setup - 模板组件引用
```vue
先定义空的ref，在模板或者组件上定义同名的ref进行绑定
不过得先渲染完成，一般在onMounted()里

当引用子组件时，子组件需要暴露difineExpose({})
------------------------
//获取模版
<script setup>
const inp = ref(null) //首先定义空的ref
console.log(inp.value)

onMounted(()=>{
    inp.value.focus() //获取到模板，并且给他聚焦方法
})
</script>

<template>
    <input ref="inp" type="test">
</template>
------------------------
//获取组件
<script setup>
const testRef = ref(null)
const getCom = ()=>{
    console.log(testRef.value)
}
</script>

<template>
    <SonCom ref="testRef"></SonCom>
    <button @click="getCom">获取组件</button>
</template>

//子组件页面：
<script setup>
const count = 99
const sayHi = ()=>{
    console.log('打招呼')
}
defineExpose({ //暴露属性和方法
    count,
    sayHi
})
</script>
```

### 8. 组合式setup - 跨层传递
```vue
顶层组件用provide，底层组件用inject
-------------------------------------
//顶层组件：
<script setup>
const count = ref(99)
provide('count', count)

provide('changeCount', (newCount)=>{ //允许让底层可修改顶层数据
    count.value = newCount
})
</script>
//底层组件：
<script setup>
const count = inject('count')
</script>
```

### 9. 组合式setup - defineOptions
```vue
因为有了setup后，没有发定义其他平级的选项（原先的选项式变为组合式）
用defineOptions({}) 可以在里面定义平级的选项
<script setup>
defineOptions({
    name:''
})
</script>
```
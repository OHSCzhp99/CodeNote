## VueRouter
---
### 1. 安装 VueRouter
安装VueRouter（用于Vue2）：  
`npm install vue-router@3`

安装VueRouter（用于Vue3）：  
`npm install vue-router@4`


使用VueRouter4：  
1.先创建专门的路由文件夹，在src中创建router文件夹，在新建index.js
```java
import { createRouter, createWebHistory } from "vue-router"; //引入路由依赖

//路由路径设置
const routes = [
    {
        path: '/my',
        name: 'my',
        component: ()=> import('../components/My.vue') //懒加载
      },
      {
        path: '/you',
        name: 'you',
        component: ()=> import('../components/You.vue')
      },
]

// 路由器
const router = createRouter({
    history: createWebHistory(), // HTML5模式
    routes,
  });

export default router; //导出路由器
```

2.（路由文件）导入（Vue中的 src-> main.js 里面）：  
```java
import router from './router' //引入自己配置路由文件夹

//并且进行挂载：
const app = createApp(App)
app.use(router) //使用router里index.js，中的Vuerouter
app.mount('#app')
```

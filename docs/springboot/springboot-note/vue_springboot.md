## Vue3 + SpringBoot
---
### 1. 使用 pnpm 创建Vue3：  
先要进行全局安装pnpm：  
`npm install -g pnpm`

pnpm创建vue：   
`pnpm create vue`  
选择必要的（4个YES）：  
VueRouter + Pinia  
ESLint + Prettier  

创建完成后cd 项目，进行依赖首次安装（下面有命令）

pnpm安装依赖：   
`pnpm install`

### 2. ESLint和prettier插件的配置：
settings.json文件：
```java
//ESLint插件 + Vscode配置 实现自动格式化修复
"editor.codeActionsOnSave": {
    "source.fixAll": true
},
//关闭保存自动格式化（防止与上面冲突）
"editor.formatOnSave": false,
```
.eslintrc.cjs文件：
```java
rules: {
    //prettier插件配置：
    //禁用格式化插件 prettier ，并且关闭settings.json中的format on save
    'prettier/prettier': [
        'warn',
        {
        singleQuote: true, //单引号
        semi: false, //无分号
        printWidth: 80, //每行宽度至多80字符
        trailingComma: 'none', //不加对象/数组最后的逗号
        endOfLine: 'auto' //换行符号不限制（win mac 不一致）
        }
    ],
    //ESLint插件配置：
    //安装ESLint插件，并配置保存时自动修复
    'vue/multi-word-component-names': [
        'warn',
        {
        ignores: ['index'] //vue组件名称多单词组成 (忽略index.vue)
        }
    ],
    'vue/no-setup-props-destructure': ['off'], //关闭props 解构的校验(props解构丢失响应式)
    'no-undef': 'error' //未定义变量错误提示
}
```

### 3. 目录文件夹结构的清理
```java
清空：
assets
components
router（只清空index.js里面routes[]）
stores
views

整理：
App.vue（只留stript、template(写个div)、style标签）
main.js（删掉assets的导入）

增加：
utils（工具相关）
api（请求接口相关）
assets -> main.scss（新建main.scss）
```
main.scss文件：
```css
body{
    margin: 0;
    background-color: #fff;
}

.fade-slide-leave-active,
.fade-slide-enter-active{
    transition: all 0.3ms;
}

.fade-slide-enter-from{
    transform: translateX(-30px);
    opacity: 0;
}

.fade-slide-leave-to{
    transform: translateX(30px);
    opacity: 0;
}
```

### 4. Router4 语法
```java
vite.config.js中加入 base: '/'（跟plugins同级）
```

### 5. ElemenetPlus 导入
安装ElemenetPlus：  
`pnpm add element-plus`

安装按需导入：（能够让组件模板之间、elementUI组件直接使用，不用手动导入）  
`pnpm add -D unplugin-vue-components unplugin-auto-import`

在vite.config.js配置文件中导入：
```js
import { defineConfig } from 'vite'
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'

export default defineConfig({
  // ...
  plugins: [
    // ...
    AutoImport({
      resolvers: [ElementPlusResolver()],
    }),
    Components({
      resolvers: [ElementPlusResolver()],
    }),
  ],
})
```

### 6. Pinia 持久化和独立维护
因为在创建vue3时已经安装了Pinia，这里不用再安装了  
但是还没安装Pinia持久化，需安装：  
`pnpm add pinia-plugin-persistedstate -D`  

在stores创建 index.js：（专门用于Pinia的统一维护、导出）  
将main.js中的关于Pinia的移除，统一交给上面的index.js管理  
并且在main.js中导入index.js  
```js
//index.js页面
import { createPinia } from 'pinia' //Pinia依赖包
import persist from 'pinia-plugin-persistedstate' //Pinia持久化

const pinia = createPinia() //创建Pinia实例
pinia.use(persist) //使用Pinia持久化

export default pinia //将index.js文件暴露出去

//将两个仓库暴露出去，让组件只用导入index.js也能使用仓库----（下面会创建这两个仓库）
export * from './modules/user' 
export * from './modules/counter'

```
```js
//main.js页面
import pinia from '@/stores/index' //导入Pinia统一管理的index.js
app.use(pinia)
```
创建仓库：  
在stores创建 modules 文件夹  
在modules -> 创建 user.js（用于用户登录携带的token）  
在modules -> 创建 counter.js（用于测试而已）  
```js
//user.js页面
import { defineStore } from 'pinia'
import { ref } from 'vue'

//用户模块 token setToken removeToken
export const useUserStore = defineStore('userStoe', () => {
  const token = ref('')
  const setToken = (newToken) => {
    token.value = newToken
  }
  const removeToken = () => {
    token.value = ''
  }

  return {
    token,
    setToken,
    removeToken
  }
})
```
组件使用：  
```js
import { useUserStore,useCountStore } from '@/stores' //组件只需导入Pinia的index.js
const userStore = useUserStore()
const countStore = useCountStore()
```

### 7. axios 请求拦截
axios配置： （读作：A可修斯） 
创建axios实例 - 基准地址、超时时间  
请求拦截器 - 携带token  
响应拦截器 - 业务失败处理、摘取核心响应数据、401处理  

安装axios：  
`pnpm add axios`

在utils中创建 request.js：
```js
import axios from 'axios'
import { useUserStore } from '@/stores'
import { ElMessage } from 'element-plus'
import router from '../router'

const baseURL = '' //基础地址

const instance = axios.create({
  baseURL,
  timeout: 10000
})

//请求拦截器
instance.interceptors.request.use(
  (config) => {
    const userStoe = useUserStore() //用仓库
    if (useUserStore.token) {
      //如果携带token则在加在请求头上
      config.headers.Authorization = userStoe.token
    }
    return config
  },
  (err) => Promise.reject(err)
)

//响应拦截器
instance.interceptors.response.use(
  (res) => {
    //返回成功为 0
    if (res.data.code === 0) {
      return res
    }
    //处理业务失败，给错误提示，抛出错误
    ElMessage.error(res.data.message || '服务异常') //利用elementPlus弹窗给出错误提示
    return Promise.reject(res.data)
  },
  (err) => {
    //处理错误：401权限不足 或者 token过期 --> 拦截到登录页
    if (err.response?.status === 401) {
      router.push('/login')
    }

    //错误的默认情况 - 只给提示
    ElMessage.error(err.response.data.message || '服务异常')
    return Promise.reject(err)
  }
)

export default instance 
export { baseURL } //这里是按需导出，可以理解为解构
```

### 8. 后台管理 - 页面路由配置
这里只记录当前步骤，后续肯定会添加更多
```js
routes: [
    //登录路由
    { path: '/login', component: () => import('@/views/login/LoginPage.vue') },
    {
        //布局路由
        path: '/',
        component: () => import('@/views/layout/LayoutContainer.vue'),
        redirect: '/manage/user',
        children: [
        {
            path: '/manage/user',
            component: () => import('@/views/manage/ManageUser.vue')
        },
        {
            path: '/manage/post',
            component: () => import('@/views/manage/ManagePost.vue')
        },
        {
            path: '/manage/menu',
            component: () => import('@/views/manage/ManageMenu.vue')
        },
        {
            path: '/manage/link',
            component: () => import('@/views/manage/ManageLink.vue')
        },
        {
            path: '/manage/person',
            component: () => import('@/views/manage/ManagePerson.vue')
        }
        ]
    }
]
```

### 9. 后台管理 - 登录页
先安装ElementPlus图标库：  
`pnpm install @element-plus/icons-vue`

这里先了解一下： 
ElementPlus的布局：  
el-row 表示一行，一行分成24份  
el-col 表示列   
:span="12"  代表在一行中，占一半  
:offset="3" 代表在一行中，左侧margin数（空出3份） 

label="请输入用户名" 代表元素的显示名字  
label-width="120px" 代表元素左侧距离  
:prefix-icon="Lock" 代表input左侧的小图标，Lock为图标名字可以在官网找  


校验规则和绑定：  
el-form => :model="ruleForm" 绑定整个form的数据对象  
el-form => :rules="rules" 绑定整个form的规则对象  
el-form-item => prop="xxx" 配置生效的是哪个规则  
表单元素 => :v-model="ruleForm.xxx" 给表单元素，绑定form的子属性  

```vue

```
## Axios
---
### 1. 安装 Axios
安装Axios：  
`npm install axios -S`

依赖导入（Vue中的 src-> main.js 里面）：
```java
import axios from 'axios' //引入axios
Vue.prototype.axios = axios  //挂载到原型上，可以全局使用
```

使用Axios：  
一般在生命周期函数create 中使用
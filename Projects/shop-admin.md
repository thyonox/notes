# 系统架构
---
1. 重复刷新后会导致请求中断，页面不能正常加载（在前置守卫中使用异步调用）
# shop-client
---
## 创建配置
---
### 使用vite创建项目
---
> [https://cn.vitejs.dev/](https://cn.vitejs.dev/)

```bash
npm create vite@latest [.]
cd my-project

npm install
npm run dev
```

### VScode插件
---
1. **Vue - Official**
2. **Vue 3 Snippets**

### 引入ElementPlus
---
> [https://element-plus.org/zh-CN/](https://element-plus.org/zh-CN/)

- 安装
    ```bash
    npm install element-plus --save
    ```
    
- 完整引入
    
    ```jsx
    import { createApp } from 'vue'
    import ElementPlus from 'element-plus'
    import 'element-plus/dist/index.css'
    import App from './App.vue'
    
    const app = createApp(App)
    
    app.use(ElementPlus)
    app.mount('#app')
    ```
    
### 引入WindiCSS

> [https://cn.windicss.org/](https://cn.windicss.org/)

- 安装
    
    ```bash
    npm i -D vite-plugin-windicss windicss
    ```
    
- 引入
    
    ```jsx
    import WindiCSS from 'vite-plugin-windicss'
    
    export default {
      plugins: [
        WindiCSS(),
      ],
    }
    ```
    
    ```jsx
    import 'virtual:windi.css'
    ```
    

### 引入VueRouter

> [https://router.vuejs.org/zh/](https://router.vuejs.org/zh/)

- 安装
    
    ```bash
    npm install vue-router@4
    ```
    
- 创建路由实例
    
    ```jsx
    import { createWebHistory, createRouter } from 'vue-router'
    
    import HomeView from './HomeView.vue'
    import AboutView from './AboutView.vue'
    
    const routes = [
      { path: '/', component: HomeView },
      { path: '/about', component: AboutView },
    ]
    
    const router = createRouter({
      history: createWebHistory(),
      routes,
    })
    
    export default router
    ```
    
    有三种不同的路由历史记录模式：
    
    - **Hash模式**：`createWebHashHistory()`
    - **Memory 模式**：`createMemoryHistory()`
    - **HTML5 模式**：`createWebHistory()`
- 注册路由插件
    
    ```jsx
    createApp(App)
      .use(router)
      .mount('#app')
    ```
    
- 根组件中引入路由页面
    
    ```jsx
    <template>
      <RouterView/>
    </template>
    ```
    

### 404页面捕获

参照VueRouter的[动态路由匹配](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html)

```jsx
const routes = [
  // 将匹配所有内容并将其放在 `route.params.pathMatch` 下
  { path: '/:pathMatch(.*)*', name: 'NotFound', component: NotFound },
  // 将匹配以 `/user-` 开头的所有内容，并将其放在 `route.params.afterUser` 下
  { path: '/user-:afterUser(.*)', component: UserGeneric },
]
```

### 别名设置

参照Vite的[别名设置](https://cn.vitejs.dev/config/shared-options.html#resolve-alias)

```jsx
import path from 'path';

export default defineConfig({
  resolve: {
    alias: {
      '~': path.resolve(__dirname,'./'),
      '@': path.resolve(__dirname,'./src')
    }
  },
  plugins: [vue(),WindiCSS()],
})
```

## 登录页开发

### Layout布局

参照ElementPlus的[Layout布局](https://element-plus.org/zh-CN/component/layout.html)

登录因为需要考虑不同设备的响应式布局，所以使用Layout布局。 Layout将页面分为24个分栏，通过 row 和 col 组件，并通过 col 组件的 span 属性就可以自由地组合布局。

### 响应式处理

参照ElementPlus的[Layout布局](https://element-plus.org/zh-CN/component/layout.html)

```jsx
<el-row>
  <el-col :lg="16" :md="12" class="left">
  </el-col>
  <el-col :lg="8" :md="12" class="reght">
  </el-col>
</el-row>
```

lg：≥1200px 响应式栅格数或者栅格属性对象 md：≥992px 响应式栅格数或者栅格属性对象 当屏幕宽度≥1200px时，左边占16，右边占8 当屏幕宽度≥992px时，左边占12，右边占12 当屏幕宽度小于992px时，为移动端布局

### 引入图标

- 安装
    
    ```bash
    npm install @element-plus/icons-vue
    ```
    
- 注册所有图标
    
    ```jsx
    import * as ElementPlusIconsVue from '@element-plus/icons-vue'
    
    const app = createApp(App)
    for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
      app.component(key, component)
    }
    ```
    
    在项目中使用图标时，可以在输入框的属性中定义，也可以通过插槽（前缀插槽）定义。
    

### 表单数据校验

1. 需要在`<el-form>`标签中添加`:rules`属性，指定规则集对象
2. 在输入框外的`<el-form-item>`标签中添加`prop`属性，对应规则集中的字段
3. 为了在点击按钮时，也能校验数据，需要在`<el-form>`标签中添加`ref`属性
4. `ref`属性值在`<script>`标签中要用`ref(null)`形式定义
5. 点击按钮触发的函数中使用`ref`属性值`.value.validate`方法进行校验，回调一个布尔参数。

### 浏览器跨域

参照vite的[代理设置](https://cn.vitejs.dev/config/server-options.html#server-proxy)

```jsx
export default defineConfig({
  server: {
    proxy: {
      // 字符串简写写法：
      // <http://localhost:5173/foo> 
      // -> <http://localhost:4567/foo>
      '/foo': '<http://localhost:4567>',
      // 带选项写法：
      // <http://localhost:5173/api/bar> 
      // -> <http://jsonplaceholder.typicode.com/bar>
      '/api': {
        target: '<http://jsonplaceholder.typicode.com>',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\\/api/, ''),
      },
      // 正则表达式写法：
      // <http://localhost:5173/fallback/> 
      // -> <http://jsonplaceholder.typicode.com/>
      '^/fallback/.*': {
        target: '<http://jsonplaceholder.typicode.com>',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\\/fallback/, ''),
      },
      // 使用 proxy 实例
      '/api': {
        target: '<http://jsonplaceholder.typicode.com>',
        changeOrigin: true,
        configure: (proxy, options) => {
          // proxy 是 'http-proxy' 的实例
        }
      },
      // 代理 websockets 或 socket.io 写法：
      // ws://localhost:5173/socket.io 
      // -> ws://localhost:5174/socket.io
      // 在使用 `rewriteWsOrigin` 时要特别谨慎，因为这可能会让
      // 代理服务器暴露在 CSRF 攻击之下
      '/socket.io': {
        target: 'ws://localhost:5174',
        ws: true,
        rewriteWsOrigin: true,
      },
    },
  },
})
```

### 引入Axios

> [http://www.axios-js.com/zh-cn/docs/](http://www.axios-js.com/zh-cn/docs/)

- 安装
    
    ```bash
    npm install axios
    ```
    
- 创建实例
    
    ```bash
    import axios from "axios";
    
    const instance = axios.create({
        baseURL: '/api', // 被vite代理
        timeout: 1000,
        headers: {'X-Custom-Header': 'foobar'}
      });
    
    export default instance
    ```
    
- 创建方法
    
    ```bash
    import axios from "@/axios";
    
    // 登录方法
    export function login(username,password){
        return axios.post('/admin/login',{
            username,
            password
        })
    }
    ```
    

### 引入useCookie

> [https://vueuse.org/](https://vueuse.org/)

- 安装
    
    ```bash
    // 先安装依赖插件
    npm i @vueuse/integrations
    npm i universal-cookie@^7
    ```
    
- 使用**useCookies**
    
    ```jsx
    import { useCookies } from '@vueuse/integrations/useCookies'
    
    const cookie = useCookies()
    cookie.set()|get()|remove()
    ```
    

### 拦截器

参照axios的[拦截器](http://www.axios-js.com/zh-cn/docs/#%E6%8B%A6%E6%88%AA%E5%99%A8)

```jsx
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```

### loading

需要为登录按钮添加`loading`状态，避免大量重复请求出现。

### 方法封装

对常用方法，尤其是第三方引入的方法进行封装，简化代码。

### 引入vuex管理用户信息

> [https://vuex.vuejs.org/zh/](https://vuex.vuejs.org/zh/)

- 安装
    
    ```bash
    npm install vuex@next --save
    ```
    
- 引入
    
    ```jsx
    import { createStore } from 'vuex'
    
    // 创建一个新的 store 实例
    const store = createStore({
      state () {
        return {
          count: 0
        }
      },
      mutations: {
        increment (state) {
          state.count++
        }
      }
    })
    export default store
    ```
    
    ```jsx
    import store from './store'
    
    const app = createApp(App)
    app.use(store)
    app.mount('#app')
    ```
    

### 配置路由守卫

参照vueRouter的[全局前置守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)

需要实现两个功能：1.没有登录强制跳转登录页2.登录之后不能重复登录

```jsx
import router from "./router";
import { getToken } from '@/utils/auth'
import { notification } from "./utils/3p";

// <https://router.vuejs.org/zh/guide/advanced/navigation-guards.html>
router.beforeEach((to, from,next) => {
    const token = getToken()
    // 未登录转到登录页
    if(!token && to.path != '/login'){
        notification('请先登录','error')
        return next({path: '/login'})
    }
    // 避免重复登录
    if(token && to.path == '/login'){
        notification('请勿重复登录','warning')
        return next({path: from.path ? from.path : '/'})
    }
    next()
  })
```

需要在`main.js`中引入此文件

## 2.12 退出登录

主页设置按钮，设置退出登录事件，在退出登录时，调用api接口方法，移除token，清除用户状态

## 2.13 全局loading进度条的实现

安装：

```jsx
npm i nprogress
```

在main.js中引入css 开启全局loading和关闭封装成两个方法，分别在全局前置守卫和全局后置钩子中进行调用

在使用 Nprogress 时，因为bar 的高度，页面会抖动，如何解决

在加载时页面消失，加载完成后出现，这是怎么实现的

## 2.14 动态页面标题实现

切换页面，标题也应该跟着发生改变 首先需要在路由中为页面设置标题，因为是全局的，所以在全局前置守卫中为页面设置标题

# 3.后台全局Layout布局开发

# shop-server


当在Vue项目中使用了别名会导致VSCode不能正常跳转，这时候需要在项目的根目录下创建一个jsconfig.json文件，指定别名
```json
{
  "compilerOptions": {
    "baseUrl": ".",           // 项目根目录
    "paths": {
      "@/*": ["src/*"],       // 定义路径别名
      "~/*": ["src/*"]
    },
    "module": "ESNext",       // 模块系统类型（通常不重要）
    "target": "ES6",          // 目标 JS 版本（可选）
    "checkJs": true           // 是否检查 JS 中的类型错误（可选）
  },
  "include": ["src/**/*"],    // 指定哪些文件是项目文件
  "exclude": ["node_modules"] // 忽略的文件夹（默认就忽略 node_modules）
}
```
## 二、常见的模块系统选择

|配置名|适用场景|生成目标|说明|
|---|---|---|---|
|`CommonJS`|Node.js (require/export)|`require` / `module.exports`|传统 Node.js 使用的|
|`ESNext`|现代浏览器、Vite、Rollup、ESM 项目|`import` / `export` 保留|不做模块转译，保留原始 ES 模块|
|`ES6` / `ES2015`|类似于 `ESNext`，但更保守|`import` / `export`|和 ESNext 类似但兼容性更好|
|`UMD`|浏览器 & Node 通用模块|即可 `require` 也可 `<script>`|很少直接写，用于库开发|
|`AMD`|老浏览器，RequireJS 模块系统|`define([...], function(){})`|基本淘汰|
|`System`|SystemJS 动态加载器使用|`System.register()`|现代开发极少使用|

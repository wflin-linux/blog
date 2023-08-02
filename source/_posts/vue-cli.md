---
title: Vue 配置
date: 2022-11-29 9:35:03
author: wflin
top: false
cover: false
toc: false
mathjax: false
summary: 
  vue 环境和打包配置
tags:
  - javascript
  - vue
categories:
  - vue打包配置
abstract: Welcome to my blog, enter password to read. 
message: 密码
password:
---
# Vue 脚手架打包注意事项



## 卸载 eslint（个人喜好）

### 终端执行命令

```txt
npm uninstall eslint --save-dev
npm uninstall @vue/cli-plugin-eslint
npm uninstall babel-eslint
npm uninstall eslint-plugin-vue
```

## 编译阶段移除所有的 console.log() 



[https://babeljs.io/docs/en/babel-plugin-transform-remove-console/](https://babeljs.io/docs/en/babel-plugin-transform-remove-console/)

### babel.config.js

#### 安装命令

>npm install babel-plugin-transform-remove-console --save-dev

#### 配置文件

* 根路径添加 babel.config.js

```javascript
// 项目开发阶段用到的babel插件
const prodPlugins = []
if (process.env.NODE_ENV === 'production') {
    prodPlugins.push('transform-remove-console')
}

module.exports = {
    'presets': [
        '@vue/cli-plugin-babel/preset'
    ],
    'plugins': [
        [
            'component',
            {
                'libraryName': 'element-ui',
                'styleLibraryName': 'theme-chalk'
            }
        ],
        // 发布产品时候的插件数组
        ...prodPlugins,
    ]
}
```

## Webpack配置

* 通过 vue.config.js 修改 webpack 的默认配置

官方文档

* [https://cli.vuejs.org/zh/config/#vue-config-js](https://cli.vuejs.org/zh/config/#vue-config-js)

### vue.config.js

创建 vue.config.js

```javascript
module.exports = {
    lintOnSave: false,
    publicPath: './',
    chainWebpack: config => {
        // 发布模式
        config.when(process.env.NODE_ENV === 'production', config => {
            config
                .entry('app')
                .clear()
                .add('./src/main-prod.js')
        })

        // 开发模式
        config.when(process.env.NODE_ENV === 'development', config => {
            config
                .entry('app')
                .clear()
                .add('./src/main-dev.js')
        })
    }
```

## Vue项目中 通过 externals 加载外部 CDN 资源

```javascript

config.set('externals', {
    vue: 'Vue',
    'vue-router': 'VueRouter',
    axios: 'axios',
    lodash: '_',
    echarts: 'echarts',
    nprogress: 'NProgress',
    'vue-quill-editor': 'VueQuillEditor'
})

config.plugin('html').tap(args => {
    args[0].isProd = true
    return args
})
})
```

### （1）整合到 vue.config.js

```javascript
module.exports = {
    lintOnSave: false,
    publicPath: './',
    chainWebpack: config => {
        // 发布模式
        config.when(process.env.NODE_ENV === 'production', config => {
            config
                .entry('app')
                .clear()
                .add('./src/main-prod.js')
        
      // 排除js 
      config.set('externals', {
        vue: 'Vue',
        'vue-router': 'VueRouter',
        axios: 'axios',
        lodash: '_',
        echarts: 'echarts',
        nprogress: 'NProgress',
        'vue-quill-editor': 'VueQuillEditor'
      })
        })

        // 开发模式
        config.when(process.env.NODE_ENV === 'development', config => {
            config
                .entry('app')
                .clear()
                .add('./src/main-dev.js')
        })
    }
```

### （2）上面改完后需要在 pblic/index.html 文件的头部，添加如下 CDN 资源引用 

* 比如 vue.js，axoius.js 的

  ```javascript
      <!-- nprogress 的样式表文件 -->
      <link rel="stylesheet" href="https://cdn.staticfile.org/nprogress/0.2.0/nprogress.min.css" />
      <!-- 富文本编辑器 的样式表文件 -->
      <link rel="stylesheet" href="https://cdn.staticfile.org/quill/1.3.4/quill.core.min.css" />
      <link rel="stylesheet" href="https://cdn.staticfile.org/quill/1.3.4/quill.snow.min.css" />
      <link rel="stylesheet" href="https://cdn.staticfile.org/quill/1.3.4/quill.bubble.min.css" />
      <!-- element-ui 的样式表文件 -->
      <link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
      
      <script src="https://cdn.staticfile.org/vue/2.5.22/vue.min.js"></script>
      <script src="https://cdn.staticfile.org/vue-router/3.0.1/vue-router.min.js"></script>
      <script src="https://cdn.staticfile.org/axios/0.18.0/axios.min.js"></script>
      <script src="https://cdn.staticfile.org/lodash.js/4.17.11/lodash.min.js"></script>
      <script src="https://cdn.staticfile.org/echarts/4.1.0/echarts.min.js"></script>
      <script src="https://cdn.staticfile.org/nprogress/0.2.0/nprogress.min.js"></script>
      <!-- 富文本编辑器的 js 文件 -->
      <script src="https://cdn.staticfile.org/quill/1.3.4/quill.min.js"></script>
      <script src="https://cdn.jsdelivr.net/npm/vue-quill-editor@3.0.4/dist/vue-quill-editor.js"></script>
  
      <!-- element-ui 的 js 文件 -->
      <script src="https://cdn.staticfile.org/element-ui/2.8.2/index.js"></script>
  ```

## 路由懒加载

**官网**：[https://router.vuejs.org/zh/guide/advanced/lazy-loading.html](https://router.vuejs.org/zh/guide/advanced/lazy-loading.html)

解释：当打包构建应用时，JavaScript 包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就会更加高效。

### 安装插件

> npm install --save-dev @babel/plugin-syntax-dynamic-import

### 声明插件

> 在 babel.config.js 配置文件中声明该插件

```js
// 项目开发阶段用到的babel插件
const prodPlugins = []
if (process.env.NODE_ENV === 'production') {
  prodPlugins.push('transform-remove-console')
}

module.exports = {
  'presets': [
    '@vue/cli-plugin-babel/preset'
  ],
  'plugins': [
    [
      'component',
      {
        'libraryName': 'element-ui',
        'styleLibraryName': 'theme-chalk'
      }
    ],
    // 发布产品时候的插件数组
    ...prodPlugins,
      
    // 懒加载插件
    '@babel/plugin-syntax-dynamic-import'
  ]
}

```

### 将路由改造成按需加载模式

**例如：**

```javascript
import Login from '../components/Login.vue'
import Home from '../components/Home.vue'
import Welcome from '../components/Welcome.vue'
```

**转变：**

```javascript
// 小括号前面：注释，后面是真正的组件位置
const Login = () => import(/* webpackChunkName: "Login_Home_Welcome" */ '../components/Login.vue')
const Home = () => import(/* webpackChunkName: "Login_Home_Welcome" */ '../components/Home.vue')
const Welcome = () => import(/* webpackChunkName: "Login_Home_Welcome" */ '../components/Welcome.vue')
```



## 项目上线

### Node 版本

* 将 vue 打包成的 dist 文件夹托管为静态资源即可

  ```javascript
  const express = require('express')
  // 创建 web 服务器
  const app = express()
  
  // 托管静态资源
  app.use(express.static('./dist'))
  
  // 启动 web 服务器
  app.listen(80,()=>{
      console.log('web server running at http://127.0.0.1')
  })
  ```

#### docker部署 (略)

### Nginx 版本

```txt
http {
    include       mime.types;
    default_type  application/json;

    sendfile        on;
    
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        # 指定前端项目所在的位置
        location / {
        root   /usr/share/nginx/html/back-vue;
        index  index.html;
        }
    }
   }
```
---
createTime: 2024-05-27
---
## vue-rote

vue 的一个插件库，专门用来实现 SPA 应用

> 注意：
> 
> 2022年2月7日后， vue-router 默认版本为 4版本
> 
> Vue2 最高仅支持 vue-router@3 版本

### SPA理解

1. 单页面 Web 应用（Single page web application） SPA
2. 整个应用只有一个完整的页面
3. 点击页面中的导航连接，不会刷新页面，只会做页面的局部更新
4. 数据需要通过 ajax 请求获取


### 路由 

1. 什么是路由
   1. 一个路由就是一组映射关系 k-v
   2. key 为路径，value 可能是 function 或 component
2. 路由分类
   1. 后端路由
      1. 理解 value 是 function 用于处理客户端提交的请求
      2. 工作过程：服务器收到一个请求，根据请求路径找到匹配的函数来处理请求，返回相应数据
   2. 前端路由
      1. 理解 value 是 component，用于展示页面内容
      2. 工作过程：当浏览器的路径改变时，对应的组件就会显示

### 基本使用

1. 安装 `vue-router` 命令：`npm i vue-router@3`，注意 Vue2最高仅仅支持 3版本
2. 在 `main.js` 应用插件： `Vue.use(VueRouter)`
```js
import Vue from 'vue'
import App from './App.vue'
import VueRouter from "vue-router"
import router from "@/router"

Vue.config.productionTip = false
Vue.use(VueRouter)

new Vue({
  render: h => h(App),
  router,
}).$mount('#app')

```
3. 编写 `router` 配置项
```js
// 该文件用于创建整个应用的路由器
import VueRouter from "vue-router"

import About from "@/components/About"
import Home from "@/components/Home"
// 创建并保留一个路由
export default new VueRouter({
    routes: [
        {
            path: '/about',
            component: About
        },
        {
            path: '/home',
            component: Home
        }
    ]
})
```
4. 实现切换 `active-class` 配置切换高亮
```html
<router-link class="list-group-item" active-class="active" to="/home">Home</router-link>
<router-link class="list-group-item" active-class="active" to="/about">About</router-link>
```

5. 指定未知
```html
<router-view/>
```

几个注意的点

1. 路由组件通常存放在 `pages` 文件夹，一般组件通常存放在 `components` 文件夹
2. 通过切换，"隐藏" 了路由的组件，默认是被销毁的，需要的时候再去挂载
3. 每个组件都有自己的 `$route` 属性，里面存储着自己的路由信息
4. 整个应用只有一个 `router` ，可以通过 `$router` 属性获取到

### 多级路由

1. 使用 `children` 配置项， 子配置路径不需要添加 `/ `
```js
{
   path: '/home',
   component: Home,
   children:[
       {
           path: 'news',
           component: HomeNews
       },
       {
           path: 'message',
           component: HomeMessage,
       }
   ]
}
```
2. 跳转要写完整路径
```html
<router-link class="list-group-item" active-class="active" to="/home/news" >News</router-link>

<router-link class="list-group-item" active-class="active" to="/home/message" >Message</router-link>
```

### 路由的query参数

1. 传递参数
```html
<router-link :to="`/home/message/detail?id=${m.id}&title=${m.title}`">{{m.title}}</router-link>

<router-link :to="{
 path: '/home/message/detail',
 query: {
   id: m.id,
   title: m.title
 }
}">
 {{m.title}}
</router-link>
```
2. 接受参数: `this.$route.query`


### 路由命名
作用: 简化路由跳转
1. 给路由命名，添加 name 属性
```js
export default new VueRouter({
    routes: [
        {
            name: 'about',
            path: '/about',
            component: About
        }]})
```

2. 简化跳转
```html
<router-link 
        :to="{name: 'homeMessageDetail'....}">
</router-link>
```

### 路由的parms参数
声明配置，在router 的 index.js 中配置
```js
{
    name: 'homeMessageDetail',
    path: 'detail/:id/:title',
    component: Detail
}
```

参数传递
```html
<router-link :to="`/home/message/detail/${m.id}/${m.title}`">{{m.title}}</router-link>
```

接收参数
```html
<li>消息编码：{{ $route.params.id }}</li>
```

注意，路由携带 params 参数，若使用 to 的对象写法，则不能使用 path 配置项，必须使用 name配置
```html
<router-link :to="{
 // path: '/home/message/detail/123/123', // 单独这样也行，就是没办法传参了
 name: 'homeMessageDetail',
 // 需要注意的是，如果使用 params 参数，不允许使用 path，只能使用 name路由
 params: {
   id: m.id,
   title: m.title
 }
}">
 {{m.title}}
</router-link>
```

### 路由props配置

作用：让路由组件更方便的收到参数

```js
{
    name: 'homeMessageDetail',
    path: 'detail/:id/:title',
    component: Detail,
    // 1、值为键值对，通过 props 传递给组件
    // props:{a:1,b:'hello'}

    // 2、值若为 ture，会把路由的 params 参数都会以 props 传递给组件
    // 但是不理 query 参数
    // props: true

    // 3、函数写法
    // props($router) {
    //     return {id: $router.query.id, title: $router.query.title}
    // }
    // 连续结构赋值
    props({query: {id, title}}) {
        return {id, title}
    }
}
```

### routerLink的replace属性

作用：控制路由跳转操作浏览器历史记录的模式

浏览器的历史记录有两种方式，分别是 push 和 replace，push 是历史追加，replace 是替换当前记录，路由跳转时候默认使用 push

开启 replace 

```html
<router-link replace ....>Home</router-link>
```

### 编程式导航
1. 不借助 router-link 实现路由跳转。通过 $router 灵活跳转

```js
this.$router.push({
  name: 'homeMessageDetail',
  query:{
    id: m.id,
    title: m.title
  }
})


this.$router.replace({
  name: 'homeMessageDetail',
  query:{
    id: m.id,
    title: m.title
  }
})
}
```

注意 push 和 replace 区别。


### 缓存路由组件

```html

让不展示的路由组件保持挂载，不被销毁

<!-- include 这里是组件名： export 下面的 name -->
<keep-alive include="HomeNews">
  <router-view/>
</keep-alive>
```

多个的写法

```html
<keep-alive :include="['HomeNews','HomeMessage']">
  <router-view/>
</keep-alive>
```

### 路由守卫

1. 对路由权限控制

#### 全局守卫

```js
// 全局前置路由守卫，每次路由切换之前被调用
router.beforeEach((to,from, next) => {
    if (to.meta.isAuth){
        if(localStorage.getItem('darkyState')==='f' ){
            next()
        }else{
            alert('浏览器本地内存中 darkyState 值不是 f')
        }
    }else{
        next()
    }
})

// 后置路由守卫
router.afterEach((to,from) =>{
    document.title = to.meta.title || 'Hello Vue'
})
```


#### 组件内路由

写在 template 里面

```js
beforeRouteEnter(to, from, next) {
 console.log('about beforeRouteEnter')
 next()
},
beforeRouteLeave(to, from, next) {
 console.log('about beforeRouteLeave')
 next()

}
```

### 路由器工作模式

hash 值不会包含在 http 请求中

## Hash模式

1. 地址中永远带着`#`号，不美观
2. 若以后地址通过第三方分享，app校验严格，则会被标记不合法
3. 兼容性比较好

## history模型

1. 地址干净、美观
2. 兼容性和hash相比略差
3. 应用部署后需要找后端人员解决，例如 `npm i connect-history-api-fallback`，一刷新浏览器丢失页面问题。



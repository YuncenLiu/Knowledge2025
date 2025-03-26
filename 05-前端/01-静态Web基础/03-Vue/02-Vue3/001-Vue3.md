# vue3-basic

学到最后，发现Vue玩的就是一个代理

## 创建Vue3工程

### 使用webpack创建

```shell
# 查看@vue/cli 版本，确保版本在 4.5.0 以上
vue --version

# 安装或者升级 @vue/cli 
npm install -g @vue/cli

# 创建
vue create vue3_yun

# 启动
cd vue_test
npm run serve
```


### 使用Vite创建

>  官方文档: [https://cn.vuejs.org/guide/quick-start.html#vite](https://cn.vuejs.org/guide/quick-start.html#vite)
> 
>  vite官网: [https://vitejs.cn/](https://vitejs.cn/)

```shell
# 创建工程
npm init vite-app vue3_yun_vite

# 启动
cd vue3_yun_vite
npm install
npm run dev
```

## 常用CompositionAPI

### setUp

1. setUp 是所有 `CompositionAPI` （组合API） 表演舞台，是 Vue3 中的新的配置项
2. 组件中所有用到的 数据、方法等都要放在 setUp 中
3. 返回值
   1. 若返回一个对象，则对象中的属性、方法，在模板中均可以直接使用
   2. 若返回渲染函数，则可以自定义渲染内容
4. 尽量不要 `vue2` 配置混用
   1. Vue2.x 配置的 （data、methods、computed...） 可以访问到 setUp 中属性，但是 setUp 不能访问 Vue2 配置
   2. 如果有重名，setUp 优先
5. setUp 不能是一个 async 函数，因为返回值不再是 return 对象，而是 promise 模板看不到 return 对象的属性
6. 后期也是可以返回一个 promise 实例，但是需要 Suspense 和异步组件配合

### ref函数

1. 定义一个响应式的数据
2. 语法 `const xxx = ref(initValue)`
   1. 创建一个包含响应式数据的 引用对象(reference对象，简称 ref 对象)
   2. Js 中操作数据 `xxx.value`
   3. 模板中读取数据，不需要 `.value`
3. 接收的数据可以是基本类型、也可以是对象类型
4. 基本类型的数据，响应式依旧靠 `Object.defineProperty()` 的 `get` 和 `set` 实现的
5. 对象类型的数据，内部使用了 Vue3 中的 `reactive` 函数

```js
 let job = ref({
   type: 'IT',
   salary: '30K'
 })

 function changeInfo(){
   name.value = '李四'
   age.value = 48
   job.value.type = 'CTO'
   job.value.salary = '100K'
 }
```

### reactive函数

定义一个 对象类型 的响应式数据（基本类型不要用它，要用 ref 函数）

语法：

```js
const 代理对象 = reactive(源对象)
```

接收一个对象、数组返回一个代理对象（proxy 对象）

reactive 定义的响应式数据是 深层次的

内部居于 ES6 的 proxy 实现，通过代理对象操作元对象内部数据进行操作

### Vue3中的响应式原理

#### Vue2的响应式

原理通过 Object.defineProperty() 对属性的读取、修改进行拦截（数据劫持）
```js
Object.defineProperties(data,'count',{
    get(){},
    set(){}
})
```
需要借助 $set 、  Vue.set 等方法才可以进行修改

存在的问题
1. 新增属性、删除属性，界面不会更新
2. 直接通过下标修改数组，界面不会自动更新

#### Vue3响应式

实现原理
1. 通过 Proxy 代理，拦截对象中任意属性的变化，包括：属性值的读写、属性的添加、属性的删除等
2. 通过 Reflect 反射，对源对象的属性进行操作
3. MDN文档中描述的 [Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 和 [Reflect](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect)
   

```js
    // Vue3 实现响应式
   const p = new Proxy(person,{
       get(target,propName){
           console.log(`${propName} 被读取`)
           return target[propName]
       },
       set(target, propName, value){
           console.log(`${propName} 属性被更新，新的内容为 ${value}`)
           target[propName] = value
       },
       deleteProperty(target, p) {
           console.log(`${p} 被删除了`)
           return delete target[p]
       }
   })


let obj = {a:1,b:2}
// Reflect.get(obj,'a') // 获取
// Reflect.set(obj,'a',6) // 修改
// Reflect.deleteProperty(obj,'a') // 删除

// 通过 Reflect 操作
    const x1 = Reflect.defineProperty(obj, 'c' ,{
        get(){
            return 3
        }
    })
    const x2 = Reflect.defineProperty(obj, 'c', {
        get(){
            return 4
        }
    })
```

### reactive对比ref

1. 从定义数据角度对比
   + ref 用来定义：`基本类型数据`
   + reactive 用来定义：`对象、数组类型`
   + ref 也可以用来定义`对象、数组`，它内部会自动通过 reactive 转化为 `代理对象`
2. 从原理角度对比
   + ref 通过 `Object.defineProperty()` 的 get 与 set 来响应式
   + reactive 通过使用 `Proxy` 来实现响应式(数据劫持) ,并通过 Reflect 操作源对象内部数据
3. 从使用角度对比
   + ref 定义的数据，操作数据需要 `.value`， 读取数据时模板中直接读取不需要 `.value`
   + reactive 定义数据，操作数据与读取数据：均不需要 `.value`

### watch函数

Vue2.x 中的 watch 功能保持一致
1. 监视 reactive 时，oldValue 无法获取，强制开启了深度监视 （deep配置失效）
2. 监视 reactive 中的某个属性仍然是对象时，deep 配置有效

```js
let sum = ref(0)
let msg = ref('你好啊')
let person = reactive({
   name: '张三',
   age: 18,
   job: {
      salary: 20
   }
})


  // 情况一：监视 ref 所定义的一个响应式数据
watch(sum, (newValue, oldValue) => {
   console.log(`总数被修改，新的值为:${newValue} 旧的值为：${oldValue}`)
}, {immediate: true})

// 情况二：监视 ref 定义的多个响应式数据
watch([sum, msg], (newValue, oldValue) => {
   console.log(`数据被修改，新的值为:${newValue} 旧的值为：${oldValue}`)
}, {immediate: true})


// 情况三，监视 reactive 定义的响应式对象，注意，此处无法正确的获取 oldValue
// 无法正常获取 oldValue
// 强制开启了深度监视（deep配置无效）
// 通过 () => person.age 监视对象内部数据，此时可以获取到 oldValue
watch(() => person.age, (newValue, oldValue) => {
   console.log('数据被修改了', newValue, oldValue)
})
// 对象模式监控多个响应值
watch([() => person.name, () => person.age], (newValue, oldValue) => {
   console.log('数据被修改了', newValue, oldValue)
})

// reactive 中的对象仍然是对象，deep 有效
watch(() => person.job, (newValue, oldValue) => {
   console.log('数据被修改了', newValue, oldValue)
}, {deep: true})
```

有几个容易出错的点，导致无法监视到

1. 如果被监视对象是 ref 基础数据类型，此时不能通过 .value 去监视，.value 会直接获取到真实数据，无法走代理，则无法成功监视
2. 如果被监视对象是 ref 对象、数组类型，则需要通过 .value 因为 ref 对象数组会再走一次 reactive，这个时候，才能成功监视到真正的代理对象

### watchEffect函数

`watch` 既要指明监视属性，也要指明监视回调

`watchEffect` 不需要指明具体哪个属性，监视的回调中用到哪个属性，就监视哪个属性

`watchEffect` 有点像 `computed`，但区别是
1. `computed` 注重计算结果的值，所以必须要写返回值
2. `watchEffect` 注重过程，不用写返回值

```js
watchEffect(()=>{
   const x1 = sum.value
   const x2 = person.job.salary
   console.log(x1,x2)
})
```

注意要引入啊 `import {watchEffect} from 'vue`

### Vue3和Vue2的生命周期区别

1. destroy 修改成了 unmount，销毁修改成卸载，对应上之前的 mount 装载
2. Vue2 在el挂载之前会先执行 beforeCreate，不太合理，Vue3 在第一步即完成挂载

Vue3的生命周期 同时支持 配置项和组合API形式，注意，组合API优先于配置项，但是有两个是组合式没有的

```js
setup() {
    // 通过组合式API使用生命周期钩子
    // beforeCreate 、 created 不支持组合式 API， setUp 就相当于这两个了。

    // 组合式 要比 配置项快一点
    // 真正开发的时候，不会同时使用 组合式 和 配置项
    onBeforeMount(()=>{
      console.log('onBeforeMount')
    })

    onMounted(()=>{
      console.log('onMounted')
    })

    onBeforeUpdate(()=>{
      console.log('onBeforeUpdate')
    })

    onUpdated(()=>{
      console.log('onUpdated')
    })

    onBeforeUnmount(()=>{
      console.log('onBeforeUnmount')
    })

    onUnmounted(()=>{
      console.log('onUnmounted')
    })
  },
  beforeCreate() {
    console.log('beforeCreate')
  },
  created() {
    console.log('created')
  },
  beforeMount() {
    console.log('beforeMount')
  },
  mounted() {
    console.log('mounted')
  },
  beforeUpdate() {
    console.log('beforeUpdate')
  },
  updated() {
    console.log('updated')
  },
  beforeUnmount() {
    console.log('beforeUnmount')
  },
  unmounted() {
    console.log('unmounted')
  }
```

### 自定义hook函数

hook 就是钩子函数

作用就是代码复用，并不是像生命周期钩子样子，通过把公共功能点单独抽离出去，需要使用的时候再把它加载进来。

hook 下创建 js 文件
```js
export default function(){
    ....
   return xxx;
}
```
引用处：

```js
import usePoint from "@/hooks/usePoint"
...
let point = usePoint()
```

### toRef

创建一个 ref 对象，其 value 指向另一个对象中的某个属性

语法： `const name = toRef(person,'name')`

将响应式对象中的第一层属性单独提供给外部使用，如果有多层，则继续在第一层之后往下找

`toRefs` 与 `toRef` 功能一致，但是可以批量创建多个 `ref` 对象，语法 `toRefs(person)`

经典写法：里面有100个就处理100个

```js
 return {
   // 只能拆第一层
   ...toRefs(person)
 }
```

## 其他CompositionAPI

### shallowReactive和shallowRef

+ shallowReactive： 只处理对象最外层属性的响应式（浅响应式）
+ shallowRef 只处理基本数据类型的响应式，不进行对象的响应式处理

使用场景

1. 如果一个对象数据，结构比较深，但变化只会影响最外层变化，使用 shallowReactive
2. 如果对象后续功能不会改变对象中的属性，而是生成新的对象来替换 shallowRef

> 但是通过和普通 ref 结合使用发现，先对 shallowRef进行操作，无反应后，再对普通 ref 进行操作，此时 shallowRef 会将之前未生效的操作一次性全部生效。

### readonly与shallowReadonly

+ readonly: 让一个响应式数据变为只读的
+ shallowReadonly: 浅层次让一个响应式数据变成只读的

应用场景：不希望数据被修改

### toRaw和markRaw

toRaw：将一个由 reactive 生成的响应式对象转化为普通对象，不可以对 ref 对象进行转化

markRaw：将对象标记为非响应式对象，当渲染大的数据源列表时，提高性能。使用场景比 toRaw 高很多。

### customRef实现防抖

翻译：custom 自定义

作用：创建一个自定义的 ref，并对其依赖项目跟踪和更新触发进行显示控制

实现防抖效果

```js
setup(){
 // 自定义 ref 函数
 function myRef(value,delay){
   return customRef((track, trigger)=>{
     let timer
     return {
       get(){
         console.log(`custom 读取 ${value} `)
         track() // 通知 Vue 追踪 value 的变化
         return value
       },
       set(newValue){
         console.log(`custom 修改 ${value} , 新的值为 ${newValue}`)
         value = newValue
         clearTimeout(timer)
         timer = setTimeout(()=>{
           trigger() // 通知 Vue 去重新解析模版
         },delay)
       }
     }
   })
 }

 // let keyWord = ref('hello')
 let keyWord = myRef('hello',500)
 return {keyWord}
}
```

### provide和inject

provide：提供
inject：注入

实现祖孙组件间通信

父组件有一个 provide 选项来提供数据，子组件有一个 inject 选项来开始使用这些数据

顶级组件：
```js
 let car = reactive({name:'大奔',price:'40W'})
 provide('car',car)
```

后代组件
```js
 let car = inject('car')
```

> 在任意一层子组件，通过直接修改响应式对象，可使得所有层组件同步修改！！！ 牛的


### 响应式数据的判断

+ isRef 检查一个值是否为一个 `ref` 对象
+ isReactive 检查一个对象是否由 `reactive` 创建并响应式代理
+ isReadonly 检查一个对象是否由 `readonly` 创建的只读代理
+ isProxy 检查一个对象是否由 `reactive` 或者 `readonly` 方法创建的代理

## Vue3新组件

### Fragment

在 Vue2 中，组件必须要由根标签

在 Vue3 ，组件可以没有根标签，内部会将多个标签包含在一个 Fragment 虚拟元素中，好处就是减少标签层级，减少内存占用


### Teleport

teleport: 传送

作用是将标签内的 DOM，传送到 to 标签下，可以是 body 下，也可以是某一个 class 下面

```html
<teleport to="body">
   ....
</teleport>
```

上面的含义就是，将 teleport 标签内的 元素渲染到 body 同层级下

![image-20240612202327108](images/README/image-20240612202327108.png)



### Suspencse

suspense：悬念

等待异步组件时渲染一些额外的内容，让用户有更好的体验感

## 其他

### 全局API的转移

Vue2.x 有许多全局API和配置
1. 注册全局组件、组测全局指令等
```js
Vue.component('MyButton',{
    data:() =>({
       count: 0
    }),
   template: '<button @click="count++">Clicked {{count}} times.</button>'
})

Vue.directive('focus',{
    inserted: el => el.focus()
})
```

Vue3.0 中这些API做了调整

| 2.x 全局API（Vue)            | 3.x 实例 API (app)                       |
|---------------------------|----------------------------------------|
| Vue.config.xxx            | app.config.xxx                         |
| Vue.config.productionTip  | <Strong style="color:red">移除</Strong>  |
| Vue.component             | app.component                          |
| Vue.directive             | app.directive                          |
| Vue.mixin                 | app.mixin                              |
| Vue.use                   | app.use                                |
| Vue.prototype             | app.config.globalProperties            |


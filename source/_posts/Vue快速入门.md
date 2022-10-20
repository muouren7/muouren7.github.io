---
title: 'Vue快速入门'
date: 2022-10-14 15:06:25
tags: [Vue,笔记]
published: true
hideInList: false
feature: 
isTop: false
---

# 基础

## 1、Demo

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <span>
            {{message}}
            {{msg.length}}
        </span>
    </div>
    <script src="./vue.js"></script>
    <script src="./main.js"></script>
</body>
</html>
```

**main.js**

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    data:{
        message:"this is a message",
        msg:["A","B","C"]
    }
})
```

## 2、v-model指令双向绑定与v-show

**v-model**实现form表单与输出的双向绑定

**v-show**当**name**里面为**空值**时，整个**span**表则不可见，当**name**里有值时，整个span标签才可见

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <div>
            <input type="text" v-model="name">
            <span v-show="name">您的名字是：{{name}}</span>
        </div>
    </div>
    <script src="./vue.js"></script>
    <script src="./main.js"></script>
</body>
</html>
```

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    data:{
        message:"this is a message",
        msg:["A","B","C"],
        name:'今晚吃什么'
    }
})
```

**v-model.trim**：去除开头结尾的空格

**v-model.number**：限定输入的类型为数字

## 3、v-for指令

**v-for**用于遍历data中的数组

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <li v-for="li in msg">{{li.name}}------{{li.price}}元</li>
    </div>
    <script src="./vue.js"></script>
    <script src="./main.js"></script>
</body>
</html>
```

**main.js**

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    data:{
        msg:[{name:"老姜",price:"10"},
            {name:"蒜",price:"5"},
            {name:"大葱",price:"9"}
        ]
    }
})
```

## 4、v-bind 标签属性值绑定指令

以下例子中，a标签中的href属性与data中的url进行了绑定

```html
<!DOCTYPE html>
<body>
    <div id="app">
        <a v-bind:href="url">点我</a>
    </div>
    <script src="./vue.js"></script>
    <script src="./main.js"></script>
</body>
```

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    data:{
        url:"https://www.taobao.com"
    }
})
```

## 5、v-on事件绑定

```html
<body>
    <div id="app">
        <button v-on:click="onClick">点我</button>
    </div>
    <script src="./vue.js"></script>
    <script src="./main.js"></script>
</body>
```

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    methods:{		//方法写在methods里，数据写在data里
        onClick :function(){
            alert("按钮被点击")
        }
    }
})
```

多个事件绑定、**v-on**里面所绑定的事件与执行函数必须存在

```html
<body>
    <div id="app">
        <button v-on="{mouseenter:onEnter,mouseleave:onOut}">点我</button>
    </div>
    <script src="./vue.js"></script>
    <script src="./main.js"></script>
</body>
```

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    methods:{
        onEnter :function(){
            console.log("鼠标移入")
        },
        onOut :function(){
            console.log("鼠标移出")
        }
    }
})
```

用于判断指定数据，如果判定为**false**，则不显示当前属性所在的标签

```html
<body>
    <div id="app">
        <div v-if="role == 'admin'">
            管理员您好
        </div>
        <div v-else-if="role == 'hr'">
            hr正在核验
        </div>
        <div v-else>
            谁也不是
        </div>
    </div>
    <script src="./vue.js"></script>
    <script src="./main.js"></script>
</body>
```

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    data:{
        role:"cc"
    }
})
```

## 7、计算属性

关键字**computed**，与methods不同的是：**computed**在数据没有发生改变的情况下，是从缓存中读取，而methods是每次都会计算一次，相比较而言，computed会更加节省性能

```html
<div id="app">
    <table border="1">
        <tr>
            <td>学科</td>
            <td>成绩</td>
        </tr>
        <tr>
            <td>数学</td>
            <td>{{math}}</td>
        </tr>
        <tr>
            <td>物理</td>
            <td>{{phs}}</td>
        </tr>
        <tr>
            <td>英语</td>
            <td>{{eng}}</td>
        </tr>
        <tr>
            <td>总分</td>
            <td>{{sum}}</td>
        </tr>
        <tr>
            <td>平均分</td>
            <td>{{avg}}</td>
        </tr>
    </table>
</div>
```

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    data:{
        math:90,
        phs:80,
        eng:30
    },
    computed:{
        sum:function(){
            return this.math + this.phs + this.eng
        },
        avg:function(){
            return Math.round(this.sum/3)  
        }
    }
})
```

## 8、组件（自定义）

关键字**Vue.component**，**template**设置组件里的具体内容

定义好组件后需要**实例化Vue**才能执行

```html
<div id="app">
    <alert></alert>
</div>
```

**全局组件**，new的每一个vue对象都可以调用

```javascript
Vue.component("alert",{
    template:'<button @click="on_click">弹弹弹</button>',
    methods:{
        on_click:function(){
            alert("Yo")
        }
    }
})
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
})
```

或者按照以下写法**(局部组件，仅在new对象内的#app上有效)**

```javascript
new Vue({
    el:"#app",
    component:{
        alert:{		//alert是自定义组件在html中的名称
            template:'<button @click="on_click">弹弹弹</button>',
        	methods:{
                on_click:function(){
                    alert("Yo")
            }
        }
    }
})
```

## 9、组件通信

**父级传子级**，关键字：**props**

例：标签属性传向Vue

```html
<div id="app">
    <user msg="muoauren"></user>
</div>
```

```javascript
Vue.component("user",{
    template:'<a href="">{{msg}}</a>',
    props:['msg'],
})
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
})
```

**子级传父级**，方法：父级中定义事件，在子级中触发

```html
<div id="app">
    <blacne></blacne>
</div>
```

```javascript
Vue.component("blacne",{
    template:`<div>
                <show @show-balance="show_balance = true">   
                </show> 
                <div v-if="show_balance">
                	您当前余额为90
            	</div>
            </div>
            `,
    data:function(){
        return{
            show_balance:false
        }
    }   
})
Vue.component("show",{
    template:'<button @click="on_click()">显示余额</button>',

    methods:{
        on_click(){
            //console.log("方法已经执行")
            this.$emit('show-balance',{a:1,b:2})	//$emit，触发show-balance事件
            
        }
    }
})
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
})
```

## 10、同级组件通信

```html
<div id="app">
    <huahua></huahua>
    <shuandan></shuandan>
</div>
```

```javascript
var Event = new Vue()		//用于传递信息
Vue.component("huahua",{
    template:`
        <div>
            我说:<input @keyup="on_change" v-model="i_said"></input>
        </div>
    `,
    methods:{
        on_change(){
            Event.$emit("huahua-said-something",this.i_said)    //触发huahua-said-something事件，传递i_said值
        }
    },
    data:function(){
        return {
            i_said:""
        }
    }
})
Vue.component("shuandan",{
    template:`
        <div>花花说：{{huahua_said}}</div>
    `,
    data:function(){
        return {
            huahua_said:""
        }
    },
    mounted:function(){		//mounted是一个钩子函数
        var me = this
        Event.$on("huahua-said-something",(data)=>{ //监听huahua-said-something事件，使用回调函数接收花花传递的信息
            me.huahua_said = data
        })
    }
})
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
})
```

## 11、过滤器

```html
<div id="app">
    <input type="text" v-model="price"/> {{price | curency}} 
</div>
```

```javascript
Vue.filter("curency",(val)=>{
    if(!val){
        val = 0
    }
    return val + "元"
})
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    data:{
        price:10
    }
})
```

## 12、自定义指令

关键字：**Vue.directive**

```html
<div id="app">
    <span v-pin="true"></span>
</div>
```

```javascript
//el为v-pin指令所在的元素，binding为v-pin指令所携带的值
Vue.directive("pin",(el,binding)=>{
    var c = binding.value;
    console.log(c)
})
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
})
```

## 13、插槽slot

对模板插入具体想要的内容

如以下：讲**插入😔**字符插入到模板中

```javascript
<div id="app">
    <panel>		<!---->
        <div slot="content">
            插入😔
        </div>
    </panel>
</div>

<template id="panel-tpl">
    <div class="panel">
            <div  style="width: 50px; height:50px ; background-color: aqua ;">
                <slot name="content">默认值</slot>
            </div>
    </div>
</template>
```

```javascript
Vue.component('panel',{
    template:"#panel-tpl",
})
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
})
```

# vue-router入门

## 1、Demo使用

**注意：**Vue与VueRouter的版本需要匹配，否则运行不了

```html
<div id="app">
    <h1>Hello App!</h1>
    <p>
        <!--使用 router-link 组件进行导航 -->
        <!--通过传递 `to` 来指定链接 -->
        <!--`<router-link>` 将呈现一个带有正确 `href` 属性的 `<a>` 标签-->
        <router-link to="/">Go to Home</router-link>
        <router-link to="/about">Go to About</router-link>
    </p>
    <!-- 路由出口 -->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view></router-view>
</div>
```

```javascript
const Home = { template: '<div>Home</div>' }
const About = { template: '<div>About</div>' }

// 2. 定义一些路由
// 每个路由都需要映射到一个组件。
// 我们后面再讨论嵌套路由。
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
]

// 3. 创建路由实例并传递 `routes` 配置
// 你可以在这里输入更多的配置，但我们在这里
// 暂时保持简单
const router = VueRouter.createRouter({
  // 4. 内部提供了 history 模式的实现。为了简单起见，我们在这里使用 hash 模式。
  history: VueRouter.createWebHashHistory(),
  routes, // `routes: routes` 的缩写
})

// 5. 创建并挂载根实例
const app = Vue.createApp({})
//确保 _use_ 路由实例使
//整个应用支持路由。
app.use(router)

app.mount('#app')

// 现在，应用已经启动了！
```

## 2、传参以及获取参数

URL:**http://127.0.0.1:5500/#/user/张三**

**方式1：**动态路由

```html
<div id="app">
    <div>
        <router-link to="/user/张三">张三</router-link>
        <router-link to="/user/李四">李四</router-link>
    </div>
    <div>
        <router-view></router-view>
    </div>
</div>
```

```javascript
const User = { template: '<div>我叫：{{ $route.params.name}} </div>' }

const routes = [
  { path:'/user/:name',component:User}
]
const router = VueRouter.createRouter({
  // 4. 内部提供了 history 模式的实现。为了简单起见，我们在这里使用 hash 模式。
  history: VueRouter.createWebHashHistory(),
  routes, // `routes: routes` 的缩写
})
const app = Vue.createApp({})
app.use(router)
app.mount('#app')
```

**方式2：**url传参

URL：**http://127.0.0.1:5500/#/user/张三?age=10**

使用**$route.query.age**获取age的值

```html
<div id="app">
    <div>
        <router-link to="/user/张三">张三</router-link>
        <router-link to="/user/李四">李四</router-link>
    </div>
    <div>
        <router-view></router-view>
    </div>
</div>
```

```javascript
const User = { template: '<div>我叫：{{ $route.params.name}}---已经{{$route.query.age}}岁了 </div>' }

const routes = [
  { path:'/user/:name',component:User}
]
const router = VueRouter.createRouter({
  // 4. 内部提供了 history 模式的实现。为了简单起见，我们在这里使用 hash 模式。
  history: VueRouter.createWebHashHistory(),
  routes, // `routes: routes` 的缩写
})
const app = Vue.createApp({})
app.use(router)
app.mount('#app')
```

## 3、子路由

在父级路由中加入children属性

```html
<div id="app">
    <div>
        <router-link to="/user/张三">张三</router-link>
        <router-link to="/user/李四">李四</router-link>
    </div>
    <div>
        <router-view></router-view>
    </div>
</div>
```

```javascript
//方式1：里面:to使用v-bind绑定to属性
const User = { template: `<div>我叫：{{ $route.params.name}}
    <router-link :to="'/user/' + $route.params.name + '/more'">更多信息</router-link>
    <router-view></router-view>
    </div>` }

//方式2：直接使用to属性跳转more，但是加入append属性
const User = { template: `<div>我叫：{{ $route.params.name}}
    <router-link to="more" append>更多信息</router-link>
    <router-view></router-view>
    </div>` }

const routes = [
  { 
    path:'/user/:name',
    component:User,
    children:[{
        path:"more",
        component:{
            template:`
                <div>
                    用户{{$route.params.name}}的详细信息
                    Leada  adahhhaiu atas shtha ahdaoihd thsissr iaii ahdha
                </div>
            `
        }
    }]
    }
]
```

## 4、手动访问与传参

暂无

## 5、命名视图

当模板中出现多个**router-view**标签时，可以给router-view加入name属性来指定不同的组件

如：

```vue
<template>
    <h1>宁当前在首页</h1>
        <router-link to="/content">详情</router-link>
        <router-link to="/main">首页</router-link>
        <router-view name="AAA"></router-view>		<!--其中的AAA与BBB分别在路由配置文件中配置-->
        <router-view name="BBB"></router-view>
</template>
<script>
    export default {
        name:"maIn"
    }
</script>
<style scoped>
</style>
```

**/router/index.js**

```javascript
const route = createRouter({
    history:createWebHashHistory(),
    routes:[
        {
            path:"/main",
            name:"main",
            components:{
                default:maIn
            }
        },
        {
            //路由路径
            path:"/content",
            name:"content",
            //跳转的组件
            components:{
                default:conTent,	//default：默认对应的组件必须得有，否则直接加名字渲染不了
                BBB:conTent		//router-view的name属性与此对应
            }
        }
    ]
})
export default route
```

## 6、导航钩子

可以理解为中间件

在**/router/index.js**文件中

```javascript
const route = createRouter({
    history:createWebHashHistory(),
    routes:[
        {
            path:"/main",
            name:"main",
            components:{
                default:maIn
            }
        },
        {
            //路由路径
            path:"/content",
            name:"content",
            //跳转的组件
            components:{
                default:conTent,
                BBB:conTent
            }
        }
    ]
})

route.beforeEach((to,from,next)=>{
    console.log(from.path)
    next()
})
```

# Axios极简入门

## 示例

```javascript
var app = new Vue({
    el:"#app",		//挂载到id为app的元素上
    data:{
        message:"this is a message",
        msg:null
    },
    mounted(){
        axios.get("./data.json").then((res)=>{
            console.log(res.data)
            this.msg = res.data		//将数据加载到Vue对象的data属性中
        })
    }
})
```

# Vue-cli脚手架极简入门

## 1、生成脚手架

```shell
vue create ProjectName
```

## 2、配置Vue-router使用

**main.js**

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import route from "./router"	//为自己创建的router文件夹，专门存放路由配置文件

createApp(App).use(route).mount('#app')
```

**App.vue**

```vue
<template>
  <div id="app">
    <h1>Vue-Router</h1>
      <router-link to="/main">首页</router-link>		<!--设置跳转组件-->
      <router-link to="/content">详情</router-link>
      <router-view></router-view>					<!--组件渲染出口-->
  </div>
</template>

<script>


export default {
  name: 'App',

}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
```

**/components/conTent.vue**

```vue
<template>
    <h1>内容详情页</h1>
</template>

<script>
    export default {
        name:"conTent"
    }
</script>

<style scoped>
</style>
```

**/router/index.js**

```javascript
import  { createWebHashHistory,createRouter } from "vue-router"
import conTent from "../components/conTent.vue"		//导入组件
import maIn from "../components/Main.vue"

const route = createRouter({
    history:createWebHashHistory(),
    routes:[
        {
            path:"/main",
            name:"main",
            component:maIn
        },
        {
            //路由路径
            path:"/content",
            name:"content",
            //跳转的组件
            component:conTent
        }
    ]
})

export default route
```
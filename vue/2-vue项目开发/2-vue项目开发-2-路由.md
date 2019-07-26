# vue-router

## 1. SPA概念 💎💎
#### 1.1 什么是单页应用？
单页应用简称SPA（Single Page Application）。整个webapp就一个html文件，里面的各个功能页面是JavaScript通过hash,或者history api来进行路由，并通过ajax拉取数据来实现响应功能。

第一次进入页面的时候会请求一个html文件，刷新清除一下。切换到其他组件，此时路径也相应变化，但是并没有新的html文件请求，页面内容也变化了。

https://h5.ele.me/  饿了么的移动端网站是典型代表

#### 1.2 什么是多页应用？
每一次页面跳转的时候，后台服务器都会给返回一个新的html文档，这种类型的网站也就是多页网站，也叫做多页应用。

#### 1.3 单页面应用的优缺点？


* 页面切换快。页面每次切换跳转时，并不需要做html文件的请求，这样就节约了很多http发送时延，我们在切换页面的时候速度很快。
* 缺点：首屏时间慢
单页应用的首屏时间慢，首屏时需要请求一次html，同时还要发送一次js请求，两次请求回来了，首屏才会展示出来。相对于多页应用，首屏时间慢。    
* SEO效果差，因为搜索引擎只认识html里的内容，不认识js的内容，而单页应用的内容都是靠js渲染生成出来的，搜索引擎不识别这部分内容，也就不会给一个好的排名，会导致单页应用做出来的网页在百度和谷歌上的排名差。  
有这些缺点，为什么还要使用SPA呢？
我们可以使用一些其它的技术来解决这些缺点，比如说服务器端渲染技术(我是SSR)，通过这些技术可以完美解决这些缺点，解决完这些问题，实际上单页面应用对于前端来说是非常完美的页面开发解决方案
    
#### 1.4 多页应用    
* 为什么多页应用的首屏时间快？    
首屏时间叫做页面首个屏幕的内容展现的时间，当我们访问页面的时候，服务器返回一个html，页面就会展示出来，这个过程只经历了一个HTTP请求，所以页面展示的速度非常快。  
* 为什么搜索引擎优化效果好（SEO）？   
搜索引擎在做网页排名的时候，要根据网页内容才能给网页权重，来进行网页的排名。搜索引擎是可以识别html内容的，而我们每个页面所有的内容都放在Html中，所以这种多页应用，seo排名效果好。   
* 但是它也有缺点，就是切换慢  
因为每次跳转都需要发出一个http请求，如果网络比较慢，在页面之间来回跳转时，就会发现明显的卡顿。  


## 2. spa原理 💎💎💎
原理是：JS会感知到url的变化，通过这一点，可以用js动态的将当前页面的内容清除掉，然后将下一个页面的内容挂载到当前页面上，这个时候的路由不是后端来做了，而是前端来做，判断页面到底是显示哪个组件，清除不需要的，显示需要的组件。这种过程就是单页应用，每次跳转的时候不需要再请求html文件了。

#### 两种实现方式：

##### hash路由: location.hash 切换 window.onhashchange 监听路径的切换：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <a href="#home">首页</a>
    <a href="#list">列表</a>
    <a href="#cart">购物车</a>
    <a href="#mine">我的</a>

    <div id="app"></div>

    <script>
    window.addEventListener("hashchange",function(){
        var hash = document.location.hash;
        console.log(hash)

        switch(hash){
            case "#home": document.querySelector("#app").innerHTML = `<h1>首页页面内容</h1>`;break;
            case "#list": document.querySelector("#app").innerHTML = `<h1>列表页面内容</h1>`;break;
            case "#cart": document.querySelector("#app").innerHTML = `<h1>购物车页面内容</h1>`;break;
            case "#mine": document.querySelector("#app").innerHTML = `<h1>我的面内容</h1>`;break;
            default: case "#home": document.querySelector("#app").innerHTML = `<h1>首页页面内容</h1>`;break;
        }
    })
    </script>
</body>
</html>
```


##### history路由
* history.pushState 切换 
* history.replaceState 替换
    * 状态对象（state object）
    * 标题（title）
    * 地址（URL）
* window.onpopstate 监听路径的切换

参考：
* https://segmentfault.com/a/1190000014120456
* https://blog.csdn.net/jx950915/article/details/80612691

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>前端路由实现</title>
</head>
<body>
    <section class="warp">
        <div class="nav">          
            <ul>
                <li><a href="javascript:void(0)" data-path="index">首页</a></li> 
                <li><a href="javascript:void(0)" data-path="news">新闻</a></li>
                <li><a href="javascript:void(0)" data-path="about">关于</a></li>
            </ul>
        </div>
        <div id="router" class="router">
            <!-- 内容加载区域 -->
        </div>
    </section>

    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
    <script>
    $('a').on('click',function(){
        console.log(this.text)
        url = this.text;

        $('#router').html('<p>'+ url +'</p>')
        history.pushState(url,null,'/'+url);
    })
    window.addEventListener('popstate',function(e){
        console.log(e.state);
        url = e.state
        $('#router').html('<p>'+ url +'</p>')

    });
    </script>
</body>
</html>
```

----

## 3. vue-router 💎💎💎💎
Vue Router 是 Vue.js 官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌。包含的功能有：

* 嵌套的路由/视图表
* 模块化的、基于组件的路由配置
* 路由参数、查询、通配符
* 基于 Vue.js 过渡系统的视图过渡效果
* 细粒度的导航控制
* 带有自动激活的 CSS class 的链接
* HTML5 历史模式或 hash 模式，在 IE9 中自动降级
* 自定义的滚动条行为

#### 1. 开始 💎💎💎
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <!-- 引入vue -->
    <script src="https://unpkg.com/vue/dist/vue.js"></script>
    <!-- 引入 vue-router -->
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
</head>
<body>
    <div id="app">
        <!-- 1.定义跳转的连接 -->
        <router-link to="/home">首页</router-link>
        <router-link to="/cat">分类</router-link>

        <!-- 2. 定义路由匹配成功后显示的内容 -->
        <router-view></router-view>
    </div>
    <script>
   const Home = { template:`<h1>首页面</h1>` }
   const Cat = { template:`<h1>分类页</h1>` }

   // 3. 创建 router 实例，然后传 `routes` 配置
    // 你还可以传别的配置参数, 不过先这么简单着吧。
   let router = new VueRouter({
       routes:[
           {path:"/home",component:Home},
           {path:"/cat",component:Cat}
       ]
   })

   // 4. 创建和挂载根实例。
    // 记得要通过 router 配置参数注入路由，
    // 从而让整个应用都有路由功能
   const app = new Vue({
       el:"#app",
       router:router
   })
    </script>
</body>
</html>
```
##### 使用脚手架搭建 💎💎💎💎💎
1. 安装vue-router  `npm install vue-router --save`
2. 定义导航链接  在 App.vue
```
<router-link to="/home">首页</router-link>
<router-link to="/cat">分类</router-link>
<router-link to="/cart">购物车</router-link>
```
 3. 接下来定义显示容器
```
<router-view></router-view>
```
4. 定义路由配置
创建 router.js
```
import VueRouter from 'vue-router';

//引入路由匹配成功后显示的组件
import Home from './components/Home.vue';
import Cat from './components/Cat.vue';
import Cart from './components/Cart.vue';

const  router = new VueRouter({
    routes:[
        {path:"/home",component:Home},
        {path:"/cat",component:Cat},
        {path:"/cart",component:Cart}
    ]
})

export default router;
```

5. 注入到vue根实例：
```
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

// 引入自定义的路由配置
import router from './router';

// 引人VueRouter
import VueRouter from 'vue-router'
//VueRouter明确使用VueRouter
Vue.use(VueRouter)

new Vue({
  // 注入到根实例
  router,
  render: h => h(App),
}).$mount('#app')
```


#### 2. 动态路由匹配 💎💎💎💎💎
1. 传参
```
<router-link class="nav" to="/user/1">user1</router-link>
<router-link class="nav" to="/user/2">use2</router-link>
```
2. 定义路由：
```
{path:"/user/:id",component:User},
```
3. 在 User组件内获取参数：
```
mounted() {
      let id = this.$route.params.id;
      console.log(id)
  }, 
```

4. 监听$route变化
```
watch: {
    $route(){
        console.log(this.$route.params.ids)          
    }
},
```
或者私用导航守卫函数：
```
// 导航守卫  钩子函数，可以监听到导航的变化
beforeRouteUpdate(to,from ,next){
    console.log('to:',to)
    console.log('from:',from)
    next();
}
```
5. 404

配置路由：
```
{path:"*",component:NotFind}
```
当访问不存在的路径是，统一返回404页面。


#### 3. 嵌套路由 💎💎💎💎💎


1. 定义导航
```
<router-link to="/list/">钢铁侠</router-link>
<router-link to="/list/thron">thron</router-link>
<router-view></router-view>
```
2. 定义路由
```
{path:"/list",component:List,
children:[
    {
        name:"list",
        path:"/list/",
        component:Iron
    },
    {
        path:"/list/thron",
        component:Thron
    }
]},
```


#### 4. 编程式导航 (js跳转) vs 声明式导航<router-link> 命名路由
```
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: '123' }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

#### 5. 动态绑定to:
1. 数据
```
data(){
 return{
   links:[
   {id:0,url:"/home",title:"首页"},
   {id:1,url:"/list",title:"list"}
 ]
 }
},
```
2. 动态产生 导航标签：
```
<router-link 
v-for="l in links" 
:to="l.url" 
:key="l.id">{{l.title}}</router-link>
```

或者：
```
<router-link :to="{name:'detail',params:{id:123}} >
首页
</router-link>
```


#### 5. 命名视图

定义视图,给视图设置名字：
```
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```
一个视图使用一个组件渲染，因此对于同个路由，多个视图就需要多个组件。确保正确使用 components 配置 (带上 s)
```
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```


#### 6. 重定向和别名
重定向：
```
{
    path:'/',redirect:"/home"
},
{
    path:"/home",
    component:Home
},
```
别名：
home的别名是 "/haha"，访问的时候，可以使用/home，也可以使用/haha,都可以匹配成功到home路由
```
{
    path:'/',redirect:"/haha"
},
{
    path:"/home",
    alias: '/haha',
    component:Home
},
```


## 4. 项目

1. 启动案例项目开发
2. 利用vue-router搭建项目SPA结构
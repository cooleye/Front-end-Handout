## 1. session 登录验证

使用session实现登陆验证的流程图
![session.png](http://note.youdao.com/yws/res/7266/WEBRESOURCEae4afa646ed54fa7c231cd27718818e6)

#### 代码实现
index.html 登录页面：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>登录页</title>
    <script
  src="https://code.jquery.com/jquery-3.4.1.min.js"
  crossorigin="anonymous"></script>
</head>
<body>
    
    <!-- 方式1：表单提交  -->
    <form action="/login" method="POST">
        用户名: <input type="text" name="username"/>  <br>
        密码: <input type="password" name="pwd"/>
        <input type="submit" value="Submit"/>
    </form>
</body>
</html>
```
在登录页，使用表单POST提交，把用户名，密码传到服务器，服务器，接收username和pwd，判断用户是否存在，如果存在，则登录成功，把用户名使用session保存下来。

```
var express = require('express');
var app = express();
var session = require('express-session');
var bodyparser = require('body-parser');

// 下面三行设置渲染的引擎模板
app.set('views', __dirname); //设置模板的目录
app.set('view engine', 'html'); // 设置解析模板文件类型：这里为html文件
app.engine('html', require('ejs').__express); // 使用ejs引擎解析html文件中ejs语法

app.use(bodyparser.json()); // 使用bodyparder中间件，
app.use(bodyparser.urlencoded({ extended: true }));

// 使用 session 中间件
app.use(session({
    secret :  'secret', // 对session id 相关的cookie 进行签名
    resave : true,
    saveUninitialized: false, // 是否保存未初始化的会话
    cookie : {
        maxAge : 1000 * 60 * 60, // 设置 session 的有效时间，单位毫秒
    },
}));

// 获取登录页面
app.get('/', function(req, res){
    res.sendFile(__dirname + '/index.html')
});

// 用户登录
app.post('/login', function(req, res){
    let username = req.body.username;
    let pwd = req.body.pwd;
    if(username == 'admin' && pwd == 'admin123'){
        req.session.userName = username; // 登录成功，设置 session,保存登录状态
        res.redirect('/home');
    }
    else{
        //res.json({ret_code : 1, ret_msg : '账号或密码错误'});// 若登录失败，重定向到登录页面
         res.redirect('/');
    }
});

// 获取主页
app.get('/home', function (req, res) {
    if(req.session.userName){  //判断session 状态，如果有效，则返回主页，否则转到登录页面
        res.render('home',{username : req.session.userName});
    }else{
        res.redirect('/');
    }
})

// 退出
app.get('/logout', function (req, res) {
    req.session.userName = null; // 删除session
    res.redirect('/');
});

app.listen(8000,function () {
    console.log('http://127.0.0.1:8000')
})
```

## 2. token 登录验证

使用token实现登陆验证的流程图

![image](http://note.youdao.com/yws/res/7262/WEBRESOURCEf625633188a330cd9d766f882595ae1d)
使用token做登陆验证的原理是，用户第一次登录的时候，把用户名和密码提交到服务器，在服务器验证用户是否存在，如果存在，则登录成功，然后使用 jsonwebtoken ，对用户名加密，生成token：
```
let content ={name:req.body.name}; // 要生成token的主题信息
let secretOrPrivateKey="jwt";// 这是加密的key（密钥）
let token = jwt.sign(content, secretOrPrivateKey, {
    expiresIn: 60*60*1  // 1小时过期
});
```
然后把登录成功的状态返回给浏览器：
```
res.json({status:1,mess:'ok',token,username})
```
浏览器拿到token后，保存起来，以后再请求其他有限制的页面或者接口时，带上token，一般会把token写到请求头上：
```
$.ajax({
    url:"http://localhost:8000/checkUser",
    type:"POST",
    headers:{
        Authorization:token
    },
    success:res=>{
        console.log(res)
    }
})
```
服务器接受到请求，获取请求头的token，验证token是否有效：
```
//每次切换都去调用此接口 用来判断token是否失效 或者过期
app.post('/checkUser',(req,res)=>{
    let token = req.get("Authorization"); // 从Authorization中获取token
    let secretOrPrivateKey="jwt"; // 这是加密的key（密钥）
    jwt.verify(token, secretOrPrivateKey, (err, decode)=> {
        if (err) {  //  时间失效的时候 || 伪造的token
            res.send({'status':10010,msg:"fail"});
        } else {
            res.send({'status':10000,msg:'success'});
        }
    })
});
```
如果token有效，就可以进行后续的操作，例如获取对数据的增删改查，然后把结果返回浏览器。
如果token无效，则返回给浏览器一个失败的状态。
浏览器接受到失败的状态后，就知道token是无效的，这是浏览器就可以跳转到登陆页，要求用户登录。

#### 完整代码：
登录页：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>登录页</title>
    <script
  src="https://code.jquery.com/jquery-3.4.1.min.js"
  crossorigin="anonymous"></script>
</head>
<body>

    <!-- 方式2：ajax提交 -->
    <form  method="POST" id="form">
        用户名: <input type="text" name="username" id="username" required/>  <br>
        密码: <input type="password" name="pwd" id="pwd" required/>
        <input type="submit" value="Submit"/>
    </form>


    <button id="authbtn"> 登录验证 </button>

    <script>

    // let token = null;
    $("#form").submit(function(e){
        // 阻止默认事件，防止页面跳转
        e.preventDefault();
   
        //获取用户名，密码
        let username = $("#username").val();
        let pwd = $("#pwd").val();

        if(username && pwd){
            $.post("http://127.0.0.1:8000/login",{username,pwd},res=>{
                console.log(res)
                //把token保存起来
                localStorage.token = res.token;
            })
        }
    })

    $("#authbtn").click(function(){
        //从 localStorage 获取token
        //调用接口，验证登录是否有效
        let token = localStorage.token;
        $.ajax({
            url:"http://localhost:8000/checkUser",
            type:"POST",
            headers:{
                Authorization:token
            },
            success:res=>{
                console.log(res)
            }
        })
    })
    
    </script>

</body>
</html>
```
服务器端代码：
```
const express = require('express');
const bodyparser = require('body-parser');
const jwt = require('jsonwebtoken');  //用来生成token
const cors = require('cors');
// const mongoose = require('mongoose');
const app = express();
//允许跨域
app.use(cors());

app.use(bodyparser.json()); // 使用bodyparder中间件，
app.use(bodyparser.urlencoded({ extended: true }));


// 获取登录页面
app.get('/', function(req, res){
    res.sendFile(__dirname + '/index.html')
});


// app.post('/getdata',(req,res)=>{

// });
//登录接口
app.post('/login',(req,res)=>{
    let username = req.body.username;
    let pwd = req.body.pwd;
    
    if(username == 'admin' && pwd == 'admin123'){
        let content ={name:req.body.name}; // 要生成token的主题信息
        let secretOrPrivateKey="jwt";// 这是加密的key（密钥）
        let token = jwt.sign(content, secretOrPrivateKey, {
            expiresIn: 60*60*1  // 1小时过期
        });
        //把token返回给浏览器
        res.json({status:1,mess:'ok',token,username})
    }else{
        res.json({status:2,mess:'密码错误'});
        return false;
    }

});
//每次切换都去调用此接口 用来判断token是否失效 或者过期
app.post('/checkUser',(req,res)=>{
    let token = req.get("Authorization"); // 从Authorization中获取token
    let secretOrPrivateKey="jwt"; // 这是加密的key（密钥）
    jwt.verify(token, secretOrPrivateKey, (err, decode)=> {
        if (err) {  //  时间失效的时候 || 伪造的token
            res.send({'status':10010,msg:"fail"});
        } else {
            res.send({'status':10000,msg:'success'});
        }
    })
});
app.listen(8000,()=>{
    console.log('http://localhost:8000')
});
````

## 3. 在vue中使用token做登陆验证
使用vue做单页应用，页面切换在前端完成。前端登录后，后端生成token,返回给前端。前端使用vuex，把token保存在store中。
之后在访问其他受限制的页面，就从store查询下时候有token，如果有的话则可以正常访问，如果没有则使用路由，跳转到登录页。

#### 1. 登录页
```
<template>
    <div>
        <h1> 登录页 </h1>
        <div>
            <input type="text"  v-model="username">
            <br>
            <input type="text" v-model="pwd">
            <button @click="login"> 登录 </button>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return {
            username:"",
            pwd:""
        }
    },
    methods:{
        login(){
            // 调用登录的action，传入用户名密码
            this.$store.dispatch("login",{username:this.username,pwd:this.pwd})
            .then( res =>{
                console.log(res)
                if(res.data.mess == 'ok'){
                    // 把得到的token保存在state中
                    this.$store.commit("saveToken",res.data.token);
                    this.$router.push('/home')
                }
            })
        }
    }
}
</script>
```

#### 2. store的实现
```
import Vue from 'vue';
import Vuex from 'vuex';
Vue.use(Vuex);
import axios from 'axios';

const store  = new Vuex.Store({
    state:{
        token:""
    },

    mutations:{
        /** 保存token */
        saveToken(state,token){
            state.token = token;
        },
        logout(state){
            state.token = ""
        }
    },

    actions:{
        /**  登录  */
        login(store,payload){
            return axios.post("http://localhost:8000/login",payload)
        }
    }
})

export default store;
```

#### 3. 页面切换
在页面切换的时候，要判断这个页面是否是限制的页面，也就是是否需要登录，可以在路由上进行标记：
```
{
    path:"/private",
    meta: {
        requireAuth: true,  //标记这个路由需要登录认证
    },
    component:PrivatePage
}
```
然后使用路由前置守卫，监听路由切换，并判断当前路由是否需要登录认证：
```
// 全局前置守卫
router.beforeEach((to, from, next) => {
    // console.log(to)
    if (to.matched.some(r => r.meta.requireAuth)) {
        if (store.state.token) {
            next();
        }
        else {
            next({
                path: '/login',
                query: {redirect: to.fullPath}
            })
        }
    }
    else {
        next();
    }
})
```
router.js 完整代码：
```
import VueRouter from 'vue-router';
import Vue from 'vue';
Vue.use(VueRouter);

import store from './store';

import LoginPage from '@/pages/login';
import HomePage from '@/pages/home';
import PrivatePage from '@/pages/private';

const router = new VueRouter({
    routes:[
        
        {path:"/login",component:LoginPage},
        {path:"/home",component:HomePage},
        {
            path:"/private",
            meta: {
                requireAuth: true,  //标记这个路由需要登录认证
            },
            component:PrivatePage
        }
    ]
})

// 全局前置守卫
router.beforeEach((to, from, next) => {
    // console.log(to)
    if (to.matched.some(r => r.meta.requireAuth)) {
        if (store.state.token) {
            next();
        }
        else {
            next({
                path: '/login',
                query: {redirect: to.fullPath}
            })
        }
    }
    else {
        next();
    }
})

export default router;
```

#### 4. axios拦截器
在调用接口的时候，需要把token带到请求头上，可以通过拦截器，拦截请求，设置请求头：
```// http request 拦截器
axios.interceptors.request.use(
  config => {
    if (store.state.token) {
      config.headers.Authorization = store.state.token
    }
    return config
  },
  err => {
    return Promise.reject(err)
  },
)
```
http.js 完整代码：
```
/**
 * 
 * http配置
 */

import axios from 'axios'
import store from './store'
import router from './router'

// axios 配置
axios.defaults.timeout = 5000
axios.defaults.baseURL = 'http://localhost'

// http request 拦截器
axios.interceptors.request.use(
  config => {
    if (store.state.token) {
      config.headers.Authorization = store.state.token
    }
    return config
  },
  err => {
    return Promise.reject(err)
  },
)

// http response 拦截器
axios.interceptors.response.use(
  response => {
    return response
  },
  error => {
    if (error.response) {
      switch (error.response.status) {
        case 401:
          // 401 清除token信息并跳转到登录页面
          store.commit("logout")
          
          // 只有在当前路由不是登录页面才跳转
          router.currentRoute.path !== 'login' &&
            router.replace({
              path: '/login',
              query: { redirect: router.currentRoute.path },
            })
      }
    }
    // console.log(JSON.stringify(error));//console : Error: Request failed with status code 402
    return Promise.reject(error.response.data)
  },
)

export default axios
```
把http.js 注入到 根实例上：
main.js代码：
```
import Vue from 'vue'
import App from './App.vue'
import router from './router';
import store from "./store";

import axios from './http';

// 将axios挂载到prototype上，在组件中可以直接使用this.axios访问
Vue.prototype.axios = axios;

new Vue({
  router,
  store,
  axios,
  render: h => h(App),
}).$mount('#app')

```





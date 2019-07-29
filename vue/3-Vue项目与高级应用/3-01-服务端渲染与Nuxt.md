# 服务端渲染

## 1. 介绍SSR（Server Side Rendering）

官网：https://ssr.vuejs.org/zh/#%E4%BB%80%E4%B9%88%E6%98%AF%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E6%B8%B2%E6%9F%93-ssr-%EF%BC%9F
#### 1. 什么是服务端渲染？

简单理解是将组件或页面通过服务器生成html字符串，再发送到浏览器，最后将静态标记"混合"为客户端上完全交互的应用程序


#### 2. 为什么使用服务器端渲染 (SSR)？

#### 3. 使用SSR的利弊

##### 优点：
1. 更利于SEO。
不同爬虫工作原理类似，只会爬取源码，不会执行网站的任何脚本。使用了React或者其它MVVM框架之后，页面大多数DOM元素都是在客户端根据js动态生成，可供爬虫抓取分析的内容大大减少。另外，浏览器爬虫不会等待我们的数据完成之后再去抓取我们的页面数据。服务端渲染返回给客户端的是已经获取了异步数据并执行JavaScript脚本的最终HTML，网络爬中就可以抓取到完整页面的信息。
2. 更利于首屏渲染
首屏的渲染是node发送过来的html字符串，并不依赖于js文件了，这就会使用户更快的看到页面的内容。尤其是针对大型单页应用，打包后文件体积比较大，普通客户端渲染加载所有所需文件时间较长，首页就会有一个很长的白屏等待时间。

#### SSR的局限：

1. 服务端压力较大。
本来是通过客户端完成渲染，现在统一到服务端node服务去做。尤其是高并发访问的情况，会大量占用服务端CPU资源；
2. 开发条件受限
在服务端渲染中，只会执行到componentDidMount之前的生命周期钩子，因此项目引用的第三方的库也不可用其它生命周期钩子，这对引用库的选择产生了很大的限制；
3. 学习成本相对较高
除了对webpack、React要熟悉，还需要掌握node、Koa2等相关技术。相对于客户端渲染，项目构建、部署过程更加复杂。

#### 4. 服务端渲染过程：
![image](http://note.youdao.com/yws/res/6988/066E298FF0414C7BBF211D06B7B7F2F8)

#### 5. 客户端渲染过程
![image](http://note.youdao.com/yws/res/6991/2E85DB14C5EA4095B5FA6AFE3DF97211)

## 2. vue ssr 基本用法：
https://ssr.vuejs.org/zh/guide/#%E5%AE%89%E8%A3%85
#### 1. 安装
创建一个文件夹，使用命令行工具进入到文件夹，执行 ` npm init -y`
然后安装vue-server-renderer：
```
npm install vue vue-server-renderer --save
```
#### 2. 使用
然后新建 base.js
```
// 第 1 步：创建一个 Vue 实例
const Vue = require('vue')
const app = new Vue({
  template: `<div>Hello World</div>`
})

// 第 2 步：创建一个 renderer
const renderer = require('vue-server-renderer').createRenderer()

// 第 3 步：将 Vue 实例渲染为 HTML
renderer.renderToString(app, (err, html) => {
  if (err) throw err
  console.log(html)
  // => <div data-server-rendered="true">Hello World</div>
})

// 在 2.5.0+，如果没有传入回调函数，则会返回 Promise：
// renderer.renderToString(app).then(html => {
//   console.log(html)
// }).catch(err => {
//   console.error(err)
// })
```
在命令行运行：
```
node base.js
```
![image](http://note.youdao.com/yws/res/7002/DAF32A036CF24F6EA33AF8163B472010)
可以看到返回也vue 模板中的内容。
结合

#### 3. 结合 Express
新建 index.js
```
const Vue = require('vue')
const server = require('express')()
const renderer = require('vue-server-renderer').createRenderer()

server.get('*', (req, res) => {
  const app = new Vue({
    data: {
      url: req.url,
      arr:[1,2,3,4]
    },
    template: `
    <div>
        <h2>访问的 URL 是： {{ url }}</h2>
        <ul>
            <li v-for="n in arr" :key="n">{{n}}</li>
        </ul>
    </div>`,
    methods: {
      increase(){
        this.count++;
      }
    },
  })

  renderer.renderToString(app, (err, html) => {
    if (err) {
      res.status(500).end('Internal Server Error')
      return
    }
    res.send(`
      <!DOCTYPE html>
      <html lang="en">
        <head><title>Hello</title></head>
        <body>
            <div id="app">
              ${html}
            </div>
        </body>
      </html>
    `)
  })
})

server.listen(8080,()=>{
    console.log('server .start....')
})
```
在命令行运行：
```
node index.js
```
启动服务器，在浏览器访问 http://localhost:8080,
可以看到 服务端渲染的页面。
![image](http://note.youdao.com/yws/res/7019/022403D30BFF44A19542AE562FD72925)


## 1. Nuxt.js介绍与安装



## 2. Nuxt.js的配置


## 3. 路由


## 4. 视图


## 5. 异步数据与资源文件


## 6. vuex状态树 ( 注意:重启服务器 )



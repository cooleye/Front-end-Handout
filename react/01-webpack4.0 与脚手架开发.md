![image](http://note.youdao.com/yws/res/8314/29FDC878AB2F4C0EBB53A7522E469A1D)

## 目录：
1. Webpack 基础介绍
2. webpack 的基本配置项讲解  💎💎
3. 入口
4. 出口
5. loaders
6. 插件
7. 模式
8. 练习
9. post-css
10. 图片
11. 学生练习
12. webpack-dev-server
13. create-react-app


课下自学：
gulp：https://www.gulpjs.com.cn/docs/
grunt：https://www.gruntjs.net/getting-started


## 1. Webpack 基础介绍

#### 前端工程化

1.前踹工程师的发展历史  
2.前端工程师的技能核    
3.前揣工程化的进化历程  
4.工程化万案架构    

#### 什么是JS项目工程化
- 版本控制
- 自动化持续继承、持续交付(CI/CD)
- 代码质量控制(QA)
- 工具
- 模块化
- 文档
- demo
- 编译过程
- 自动化处理每次push, tag, release的任务队列

1. 安装
    - 安装 : npm命令行工具
    - 安全审计：npm audit
2. Lint
    - 格式检查: eslint/stylelint
    - 格式化: prettier
3. 测试
    - 测试套装: jest / mocha / ava / kamar
    - 代码覆盖量: nyc / codecov / coveralls
4. 构建
    - 转换器: babel / TS / flow
    - 预处理器: sass / less / postcss
    - 代码混淆: uglify-js / terser
    - 打包及tree shaking: webpack / rollup / parcel
    - 压缩(gzip等)
    - 复制 / 删除 / 移动文件
    - 检查打包文件的大小
    - 移除无用的代码
5. push
    - 交付: git
    - 发布: npm
6. 部署
    - 服务器
    - Pages: git pages
    - 云服务器: aliyun / qcloud / aws
    - Story Book

#### webpack
webpack 是一个现代 JavaScript 应用程序的静态模块打包器(module bundler)。当 webpack 处理应用程序时，它会递归地构建一个依赖关系图(dependency graph)，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle

其它相似打包工具还有rollup.js 、 parcel、FIS等

#### 中文文档
https://www.webpackjs.com/



## 2. webpack 的基本配置项讲解
 
#### webpack的核心概念：

- 入口(entry)
- 输出(output)
- loader
- 插件(plugins)
- 模式

#### 安装
 webpack 命令行相关的内容都迁移到 webpack-cli，所以除了 webpack 外，我们还需要安装 webpack-cli：
```
npm install webpack  webpack-cli -g  --save-dev
```

#### 配置文件

webpack.config.js

##### 配置项详解：https://www.webpackjs.com/configuration/#%E9%80%89%E9%A1%B9

#### 入门示例：
1. 创建项目 `project/`
2. 创建目录结构如下

--| project         
------| src         
---------| bar.js           
---------| index.js         
------| webpack.config.js      
------| index.html

bar.js
```
export default function bar(x,y) {
    return x+y;
  }
```
index.js
```
import bar from './bar';

let r = bar(3,5);

document.write(r)
```
index.html
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="dist/bundle.js"></script>
</head>
<body>
</body>
</html>
```
webpack.config.js
```
const path = require('path');

module.exports = {
  mode: 'development',  //模式
  entry: './src/index.js', //入口
  //出口
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
};
```


## 3. 入口

#### 基本写法
```
module.exports = {
  entry: './path/to/my/entry/file.js'
};
```
#### 对象写法
```
module.exports = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
};
```

#### 多页面
```
const config = {
   entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js'
  }
};
```

## 4. 出口
==注意，即使可以存在多个入口起点，但只指定一个输出配置。==

#### 基本用法
```
module.exports = {
  output: {
    filename: 'bundle.js',
    path: '/home/proj/public/assets'
  }
};
```
#### 多个入口起点
如果配置创建了多个单独的"chunk"，则应该使用占位符(substitutions)来确保每个文件具有唯一的名称。
```
const path = require('path');

module.exports = {
    mode: 'development',
    //配置入口
    entry: {
        app: './src/index.js',
        search: './src/search.js'
    },
    //配置出口
    output: {
        filename: '[name].js',
        path: __dirname + '/dist'
    }
};
```
#### 添加hash
```
output: {
    filename: '[name].[hash].js',
    path: __dirname + '/dist'
}
```
## 5. 模式
提供 mode 配置选项，告知 webpack 使用相应模式的内置优化。
```
module.exports = {
  mode: 'production'
};
```


## 6. loaders 配置

loader 用于对模块的源代码进行转换。loader 可以使你在 import 或"加载"模块时预处理文件。
例如，加载css文件
```
npm install --save-dev css-loader style-loader
```
配置文件：
```
module: {
     // 模块规则（配置 loader、解析器等选项）
    rules: [
      {
        // 这里是匹配条件，每个选项都接收一个正则表达式或字符串
        test: /\.css$/,
        // 应用多个 loader 和选项
        use: [
            // loader名称
            //style-loader 通过注入<style>标签将CSS添加到DOM
          { loader: 'style-loader' },
          //css-loader 解释(interpret) @import 和 url() ，会 import/require() 后再解析(resolve)它们
          {
            loader: 'css-loader',
            // loader的选项
            options: {
            /**启用/禁用 CSS 模块
            * 注意，如果设置为true，则类名会被编译成hash值
            * 使用的时候需要： import styles from './style.css';
            * let div = document.createElement('div');
            * div.innerHTML = "hello div";
            * div.setAttribute("class",styles.box)
            */
              modules: true  
            }
          }
        ]
      }
    ]
  }
```
##### css-loader详解：https://cloud.tencent.com/developer/section/1477514

在文件中加载css：

style.css:
```
body{
    background-color: #afa;
}

.box{
    width: 100px;
    height: 100p;
    border: solid 2px red;
}
```
search.js:
```
import './style.css';
let div = document.createElement('div');
div.innerHTML = "hello div";
div.setAttribute("class","box")

console.log(document.body)
document.body.appendChild(div)
```
还可以 内联使用和通过cli使用（不常用）


## 7. 插件配置

webpack 插件是一个具有 apply 属性的 JavaScript 对象。apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个编译生命周期访问。
例如我们自定义一个插件，让他记录webpack构建开始的过程：
```
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

class ConsoleLogOnBuildWebpackPlugin {
    apply(compiler) {
        compiler.hooks.run.tap(pluginName, compilation => {
            console.log("webpack 构建过程开始！");
        });
    }
}

module.exports = ConsoleLogOnBuildWebpackPlugin;
```


使用方法：在 webpack 配置中，向 plugins 属性传入 new 实例
```
...
 plugins: [
    new ConsoleLogOnBuildWebpackPlugin()
  ]
...
```

#### HtmlWebpackPlugin
HtmlWebpackPlugin 插件可以使用一个模板生成html文件。这对于在文件名中包含每次会随着编译而发生变化哈希的 webpack bundle 尤其有用。

例如我们使用了多文件入口，并且文件名称上加上了hash：
1. 安装 `npm install --save-dev html-webpack-plugin`
2. 修改webpack.config.js，添加：
```
...
plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
]
...
```
在src/目录下新建index.html文件，然后运行 webpack，执行构建，插件就会使用src/index.html在dist/目录下自动生成index.html,并且把构建好的js自动添加到html文件中。 


## 8. 学员练习 
1. 练习webpack 的使用，并且简单实现单页面配置与 多页面配置
2. 练习使用css-loader和style-loader，实现简单页面样式效果



## 9. post-css处理css

#### postcss介绍
postcss 一种对css编译的工具，类似babel对js的处理，常见的功能如：    
1 . 使用下一代css语法   
2 . 自动补全浏览器前缀  
3 . 自动把px代为转换成rem（rem不熟悉的，点这）  
4 . css 代码压缩等等

postcss  只是一个工具，本身不会对css一顿操作，它通过插件实现功能，autoprefixer 就是其一。   

#### 与less sass的区别

1. less sass 是预处理器，用来支持扩充css语法。
2. postcss 既不是预处理器也不是后处理器，其功能比较广泛，而且重要的一点是，postcss可以和less/sass结合使用。
3. postcss 鼓励开发者使用规范的CSS原生语法编写源代码，支持未来的css语法，就像babel支持ES6。

虽然可以结合less/sass使用，但是它们还是有很多重复功能，用其中一个基本就 ok 了

#### 使用
1. 安装
```
npm install --save-dev  postcss-loader
```
2. postcss配置文件
在项目根目录新建 postcss.config.js
```
module.exports = {
    plugins: {
    
    }
}
```
3. webpack.config.js
```
...省略code...
module: {

     // 模块规则（配置 loader、解析器等选项）
    rules: [
      {
        // 这里是匹配条件，每个选项都接收一个正则表达式或字符串
        test: /\.css$/,
        // 应用多个 loader 和选项
        use: [
            // loader名称
          { loader: 'style-loader' },
          {
            loader: 'css-loader',
            // loader的选项
            options: {
              modules: true  //启用/禁用 CSS 模块
            }
          },
          //使用postcss-loader
          {loader:'postcss-loader'}
        ]
      }
    ]
  },
  ...省略code...
```
4. 常用的postcss插件
- autoprefixer
- postcss-cssnext
- postcss-pxtorem

webpack.config.js 配置文件
```
watch: true,   // 监听修改自动打包
module: {

 // 模块规则（配置 loader、解析器等选项）
rules: [
  {
    // 这里是匹配条件，每个选项都接收一个正则表达式或字符串
    test: /\.css$/,
    // 应用多个 loader 和选项
    use: [
        // loader名称
      { 
        loader: 'style-loader'
      },
      {
        loader: 'css-loader',
        // loader的选项
        options: {
          modules: true  //启用/禁用 CSS 模块
        }
      },
      //使用postcss-loader
      {
          loader:'postcss-loader',
          options:{
            plugins: [
                require('autoprefixer')(),
                require("postcss-cssnext")(),
                require("postcss-pxtorem")()
            ]
          }
      }
    ]
  }
]
},
```
postcss-pxtorem文档：https://www.npmjs.com/package/postcss-pxtorem


## 10. 图片
#### 1. file-loader
安装：
```
npm i -D file-loader
```
配置：
```
{
    test: /\.(png|jpg|gif|svg)$/,
    use: ['file-loader']
}
```
或者：
```
{
    test: /\.(png|jpg|gif|svg)$/,
    loader: 'file-loader'
}
```
file-loader 配置选项：
```
{
    test: /\.(png|jpg|gif|svg)$/,
    loader: 'file-loader',
    options: {
        name: 'images/[name].[ext]'
    }
}
```
file-loader能自动识别CSS代码中的图片路径并将其打包至指定目录，在js中需要先将图片资源加载进来，然后再将其作为图片路径添加至图片对象。具体代码如下：
```
var imgSrc = require('../images/dog.png');
var img = new Image();
img.src = imgSrc;
document.body.appendChild(img);
```

#### 2. url-loader
url-loader 也可以对图片进行处理，还可以对小于某个大小的图片进行base64格式的转化处理。
安装：
```
npm i -D url-loader
```
配置：
```
{
    test: /\.(png|jpg|gif|svg)$/,
    loader: 'url-loader',
    options: {
        name: './images/[name].[ext]',
        limit: 8192
    }
}
```
这里limit属性的作用就是将小于8192B（8.192K）大小的图片转成base64格式，而大于这个大小的图片将会以file-loader的方式进行打包处理。
如果不写limit属性，那么url-loader就会默认将所有图片转成base64。

## 11. 学生练习
查看文档，学习如何加载json文件。

## 12. webpack-dev-server
dev-server 用于启动一个本地开发服务器。

安装：
1. 全局安装
```
npm i webpack-dev-server -g
```
2. 局部安装
```
npm i webpack-dev-server -D
```

常用配置选项：

```
  devServer:{
    host:"localhost", //主机地址
    port:8080,  //端口
    //配置代理
    proxy: {
      "/api": "http://localhost:3000"
    },
    //此路径下的打包文件可在浏览器中访问
    publicPath:"/asstes/"
  }
```
publicPath:
假设服务器运行在 http://localhost:8080 并且 output.filename 被设置为 bundle.js。默认 publicPath 是 "/"，所以你的包(bundle)可以通过 http://localhost:8080/bundle.js 访问。

可以修改 publicPath，将 bundle 放在一个目录：
```
publicPath: "/assets/"
```
你的包现在可以通过 http://localhost:8080/assets/bundle.js 访问。

使用webpack-dev-server 启动服务器：
```
webpack-dev-server
```
或者添加搭配package.json的script中：
```
"scripts": {
    "build": "webpack",
    "dev":"webpack-dev-server",
    "test": "echo \"Error: no test specified\" && exit 1"
},
```


## 13. react 官方脚手架 create-react-app 的使用

#### Github:https://github.com/facebook/create-react-app

#### 1. 安装：
```
npm install -g create-react-app
```

#### 2. 创建项目：

```
create-react-app react-demo
```
#### 3. 运行
```
npm start
```
#### 4. 构建
```
npm run build
```
#### 5. 生成配置文件
```
npm run eject
```
注意，这一步是不可逆的

#### 6. 初始项目目录结构

my-app          
|── README.md           
|── node_modules            
|── package.json            
|── .gitignore          
|── public          
────├── favicon.ico         
────├── index.html          
────└── manifest.json           
── src         
────├── App.css     
────├── App.js          
────├── App.test.js     
────├── index.css           
────├── index.js            
────├── logo.svg            
────└── serviceWorker.js            

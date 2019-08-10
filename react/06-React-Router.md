## 1. React Router 介绍
* 文档：https://react-guide.github.io/react-router-cn/docs/Introduction.html
* github:https://github.com/ReactTraining/react-router

React Router被拆分成三个包：`react-router`,`react-router-dom`和`react-router-native`。`react-router`提供核心的路由组件与函数。其余两个则提供运行环境（即浏览器与react-native）所需的特定组件。

进行网站（将会运行在浏览器环境中）构建，我们应当安装`react-router-dom`。`react-router-dom`暴露出`react-router`中暴露的对象与方法，因此只需要安装并引用`react-router-dom`即可

安装： 使用React Router
```
npm install --save react-router-dom
```

## 2. 核心概念

#### 1. 路由器(Router)组件
* <BrowserRouter>
* <HashRouter>
用<BrowserRouter>组件用来管理动态请求，而<HashRouter>被用于静态网站。

路由器组件无法接受两个及以上的子元素。通常会把<BrowserRouter>作为根组件，<App>组件作为其唯一的子组件，其余内容显示在App组件中。
##### 使用示例：
```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

import {BrowserRouter as Router} from 'react-router-dom';

ReactDOM.render(
    <Router>
        <App/>
    </Router>
    , document.getElementById('root'));
```

#### 2. 路由(Route)组件
* `<Route>`组件是React Router中主要的结构单元。
* `<Route>`接受一个数为string类型的path，该值路由匹配的路径名的类型。例如：`<Route path='/roster'/>`会匹配以`/roster`开头的路径名。在当前path参数与当前location的路径相匹配时，路由就会开始渲染React元素。若不匹配，路由不会进行任何操作。
```
<Route path='/roster'/>
// 当路径名为'/'时, path不匹配
// 当路径名为'/roster'或'/roster/2'时, path匹配
// 当你只想匹配'/roster'时，你需要使用"exact"参数
// 则路由仅匹配'/roster'而不会匹配'/roster/2'
<Route exact path='/roster'/>
```

当一个路由的path匹配成功后，路由用来确定渲染结果的参数有三种。只需要提供其中一个即可。

* component ： 一个React组件。当带有component参数的route匹配成功后，route会返回一个新的元素，其为component参数所对应的React组件（使用React.createElement创建）。
* render ： 一个返回React element的函数。当匹配成功后调用该函数。该过程与传入component参数类似，并且对于行级渲染与需要向元素传入额外参数的操作会更有用。
* children ： 一个返回React element的函数。与上述两个参数不同，无论route是否匹配当前location，其都会被渲染。
```
<Route path='/page' component={Page} />
const extraProps = { color: 'red' }
<Route path='/page' render={(props) => (
  <Page {...props} data={extraProps}/>
)}/>
<Route path='/page' children={(props) => (
  props.match
    ? <Page {...props}/>
    : <EmptyPage {...props}/>
)}/>
```

#### 3. 导航组件
导航组件`<Link>`用来提供导航链接

`<Link>`使用'to'参数来描述需要定位的页面。它的值即可是字符串也可是location对象（包含pathname，search，hash与state属性）。如果其值为字符串将会被转换为location对象。
```
<Link to={{ pathname: '/roster' }}>Player</Link>
```

#### 4. location对象
locations 是一个含有描述URL不同部分属性的对象
```
// 一个基本的location对象
{ pathname: '/', search: '', hash: '', key: 'abc123' state: {} }
```

## 3. 路由示例
```
import React from 'react';
import {Link,Route} from 'react-router-dom';

const Home = () => <h1> Home Page</h1>

const List = () => <h1> List Page</h1>


function App() {
  return (
    <div className="App">

        <ul>
        {/** 使用Link 组件创建导航链接 */}
          <li><Link to="/"> 首页 </Link></li>
          <li><Link to="/list"> 列表 </Link></li>
        </ul>

        <div>
            {/** Route 组件匹配路径，渲染子组件 */}
            <Route path='/' exact component={Home} />
            <Route path='/list' component={List} />
        </div>
        
    </div>
  );
}

export default App;
```

## 4. 路由嵌套
二级路由嵌套，只需要在`<Link>` 的path上拼接二级路径即可，
同时 `<Route>`的path也要与之对应，匹配上就会在
一级路由页面下显示二级路由页面。
##### 示例代码：
```
import React from 'react';
import {Link,Route} from 'react-router-dom';

const Home = () => <h1> Home Page</h1>

const List = () => <h1> List Page</h1>

const IronMan = () =>  <h2> 钢铁侠！！！ </h2>

const Thor = () =>  <h2> 雷神！！！ </h2>

const User = () => (
  <div>
      <h1> super hero </h1>
      {/**二级路由导航 */}
      <Link to="/user/iron"> 钢铁侠 </Link>
      <Link to="/user/thor"> 雷神 </Link>
      {/**二级路由 */}
      <Route path='/user/iron' component={IronMan}/>
      <Route path='/user/thor' component={Thor}/>
  </div>
)


function App() {
  return (
    <div className="App">

        <ul>
        {/** 使用Link 组件创建导航链接 */}
          <li><Link to="/"> 首页 </Link></li>
          <li><Link to="/list"> 列表 </Link></li>
          <li><Link to="/user"> 用户 </Link></li>
        </ul>

        <div>
            {/** Route 组件匹配路径，渲染子组件 */}
            <Route path='/' exact component={Home} />
            <Route path='/list' component={List} />
            <Route path='/user' component={User}/>
        </div>
        
    </div>
  );
}

export default App;

```

## 5. 路由传参

#### 1. 路径参数
- 在路由<Route>的path上使用`/:参数`，例如：` /:id`代表参数。
- 在组件中从 props.match.params.id获取参数

##### 示例代码：
在导航组件上`<Link>`上传参：
```
<li><Link to="/article/666"> 路径参数 </Link></li>
```
使用路由组件匹配：
```
<Route path='/article/:id' component={Article}/>
```
在组件`<Article>`中获取参数：
```
const Article = props => <h2> 参数是：{props.match.params.id}</h2>
```

##### 优点:简单快捷,并且，在刷新页面的时候，参数不会丢失。
##### 缺点:只能传字符串，并且，如果传的值太多的话，url会变得长而丑陋。

#### 2. query参数
```
<Link to={{pathname:"/article/888",search:"?name=zs"}}> query参数 </Link>
```
获取参数：
```
  let params = new URLSearchParams(props.location.search);
  let name = params.name;
```

#### 3. state 参数
```
<Link to={{pathname:"/article/999",state:{page:2,size:10}}}> state参数 </Link>
```
获取参数：
```
props.location.state
```


## 6. NavLink 组件
当url匹配上时，可以为当前导航组件添加样式属性
```
<NavLink to="/faq" activeClassName="selected"> FAQs</NavLink>
```
添加 activeStyle属性
```
<NavLink   to="/faq"
  activeStyle={{
    fontWeight: "bold",
    color: "red"
  }}
>
  FAQs
</NavLink>
```

## 7. Switch组件
当有多个url匹配成功时，Switch组件会选出第一个匹配的组件来渲染
```
<div className="App">
    <ul>
    {/** 使用Link 组件创建导航链接 */}
      <li><NavLink to="/" exact activeClassName="selected"> 首页 </NavLink></li>
      <li><NavLink to="/list" activeClassName="selected"> 列表 </NavLink></li>
      <li><NavLink to="/user" activeClassName="selected"> 用户 </NavLink></li>
      <li><NavLink to="/article/666" activeClassName="selected"> 路径参数 </NavLink></li>
      <li><NavLink to="/123" activeClassName="selected"> 电影 </NavLink></li>
    </ul>

    <div>
          <Switch>
            {/** Route 组件匹配路径，渲染子组件 */}
            <Route path='/' exact component={Home} />
            <Route path='/list' component={List} />
            <Route path='/user' component={User}/>
            <Route path='/article/:id' component={Article}/>
            <Route path='/:movie' component={Movie}/>
          </Switch>
    </div>
</div>
```
在上面的代码中，如果不使用Switch组件，`/:movie`匹配前面所有的路径。

另外，还可以使用 
```
<Route component={NoMatch}/>
```
显示一个404页面，当前面所有路由都没有匹配上的时候，就会显示<NoMatch>

## 8. 重定向 Redirect 
Redirect 组件用来实现路由重定向
当url匹配上时，重定向到另一个路由
<Redirect to="/somewhere/else" />
```
 <Route path='/' exact component={Home}>
  <Redirect to="/home"/>
</Route>
<Route path='/home' exact component={Home}/>
```

## 9. 编程式导航

- history.push()
- history.replace()
- history.go()
- history.goBack()
- history.goForward()
- 
```
this.props.history.push({"pathname":"article/888",search:"?name=zs"})
```

## 10. 实现路由钩子
路由在切换是，会引起prop的改变，于是我们可以结合组件的生命周期函数和路由的属性，实现路由钩子。
当路由切换时，会触发组件的生命周期函数：
- componentDidMount
- componentWillUnmount
- componentWillReceiveProps

##### 示例代码：
```
//User.js
import React, { Component } from 'react';
import {Route,NavLink} from 'react-router-dom';
import IronMan from './IronMan';
import Thor from './Thor';

class User extends Component {

    constructor(){
        super();
        this.state = {
            hero:"钢铁侠"
        }
    }
    // 组件首次显示时，显示标题
    componentDidMount(){
        this.updateTtile(this.props)
        
    }
    // 子组件切换时，更新标题
    componentWillReceiveProps(props){
        this.updateTtile(props)
    }
    // 当路由切换时，更新标题显示
    updateTtile(props){
        if(props.location.pathname === '/user/thor'){
            this.setState({
                hero:"雷神"
            })
        }else{
            this.setState({
                hero:"钢铁侠"
            })
        }
    }

    render() {
        return (
            <div>
                {/** 子路由切换，更新这里的标题显示 */}
                <h1> super hero {this.state.hero} </h1>
                <NavLink to="/user/iron" activeClassName="selected"> 钢铁侠 </NavLink>
                <NavLink to="/user/thor" activeClassName="selected"> 雷神 </NavLink>

                <Route path='/user/iron' component={IronMan}/>
                <Route path='/user/thor' component={Thor}/>

                <button onClick={()=>{
                    this.props.history.push({"pathname":"article/888",search:"?name=zs"})
                }}> to home </button>
            </div>
        );
    }
}

export default User;
```
子组件
```
//Iron.js
import React, { Component } from 'react';

class IronMan extends Component {

    componentDidMount(){
        console.log("显示钢铁侠。。。")
    }
    componentWillUnmount(){
        console.log('钢铁侠消失')
    }
    render() {
        return (
            <div>
                <h2> 钢铁侠！！！ </h2>
            </div>
        );
    }
}

export default IronMan;
```

## 11. withRouter
让被修饰的组件可以从属性中获取history,location,match

路由组件可以直接获取这些属性，而非路由组件就必须通过withRouter修饰后才能获取这些属性了，比如:
App组件是非路由组件：
```
import { withRouter } from "react-router";

class App extends Component{
     componentWillReceiveProps(props){
        console.log(props)
    }
}

export default withRouter(App);
```
这样再路由切换时，App中也能拿到路由信息了。
其原理是，withRouter是一个高阶组件，在函数内部，把路由信息history,location,match通过props传递给组件。

## 12. 学员利用练习时间搭建简易的 react SPA 应用，练习各个 API



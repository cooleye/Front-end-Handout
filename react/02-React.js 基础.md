## 1. react 基础

#### 1.1 说明

React 起源于 Facebook 的内部项目，因为该公司对市场上所有 JavaScript MVC 框架，都不满意，就决定自己写一套，用来架设Instagram 的网站。做出来以后，发现这套东西很好用，就在2013年5月开源了。

React官网：
- 英：https://reactjs.org/
- 中：https://zh-hans.reactjs.org
- github：https://github.com/facebook/react

#### 1.2 React与传统MVC的关系

轻量级的视图层**库**！*A JavaScript library for building user interfaces*

React不是一个完整的MVC框架，最多可以认为是MVC中的V（View），甚至React并不非常认可MVC开发模式；React 构建页面 UI 的库。可以简单地理解为，React 将将界面分成了各个独立的小块，每一个块就是组件，这些组件之间可以组合、嵌套，就成了我们的页面。

#### 1.3 React高性能的原理：

在Web开发中我们总需要将变化的数据实时反应到UI上，这时就需要对DOM进行操作。而复杂或频繁的DOM操作通常是性能瓶颈产生的原因（如何进行高性能的复杂DOM操作通常是衡量一个前端开发人员技能的重要指标）。

React为此引入了虚拟DOM（Virtual DOM）的机制：在浏览器端用Javascript实现了一套DOM API。基于React进行开发时所有的DOM构造都是通过虚拟DOM进行，每当数据变化时，React都会重新构建整个DOM树，然后React将当前整个DOM树和上一次的DOM树进行对比，得到DOM结构的区别，然后仅仅将需要变化的部分进行实际的浏览器DOM更新。而且React能够批处理虚拟DOM的刷新，在一个事件循环（Event Loop）内的两次数据变化会被合并，例如你连续的先将节点内容从A-B,B-A，React会认为A变成B，然后又从B变成A  UI不发生任何变化，而如果通过手动控制，这种逻辑通常是极其复杂的。

尽管每一次都需要构造完整的虚拟DOM树，但是因为虚拟DOM是内存数据，性能是极高的，而对实际DOM进行操作的仅仅是Diff分，因而能达到提高性能的目的。这样，在保证性能的同时，开发者将不再需要关注某个数据的变化如何更新到一个或多个具体的DOM元素，而只需要关心在任意一个数据状态下，整个界面是如何Render的。

#### 1.4 React Fiber:

在react 16之后发布的一种react 核心算法，**React Fiber是对核心算法的一次重新实现**(官网说法)。之前用的是diff算法。

在之前React中，更新过程是同步的，这可能会导致性能问题。

当React决定要加载或者更新组件树时，会做很多事，比如调用各个组件的生命周期函数，计算和比对Virtual DOM，最后更新DOM树，这整个过程是同步进行的，也就是说只要一个加载或者更新过程开始，中途不会中断。因为JavaScript单线程的特点，如果组件树很大的时候，每个同步任务耗时太长，就会出现卡顿。

React Fiber的方法其实很简单——分片。把一个耗时长的任务分成很多小片，每一个小片的运行时间很短，虽然总时间依然很长，但是在每个小片执行完之后，都给其他任务一个执行的机会，这样唯一的线程就不会被独占，其他任务依然有运行的机会。

#### 1.5 React的特点和优势

##### 1. 虚拟DOM  
我们以前操作dom的方式是通过document.getElementById()的方式，这样的过程实际上是先去读取html的dom结构，将结构转换成变量，再进行操作       
而reactjs定义了一套变量形式的dom模型，一切操作和换算直接在变量中，这样减少了操作真实dom，性能真实相当的高，和主流MVC框架有本质的区别，并不和dom打交道

##### 2. 组件系统 
react最核心的思想是将页面中任何一个区域或者元素都可以看做一个组件 component       

##### 那么什么是组件呢？          
组件指的就是同时包含了html、css、js、image元素的聚合体          
使用react开发的核心就是将页面拆分成若干个组件，并且react一个组件中同时耦合了css、js、image，这种模式整个颠覆了过去的传统的方式  

##### 3. 单向数据流           
其实reactjs的核心内容就是数据绑定，所谓数据绑定指的是只要将一些服务端的数据和前端页面绑定好，开发者只关注实现业务就行了

##### 4. JSX  语法
在react中，我们使用render函数来构建组件的dom结构性能较高，因为省去了查找和编译模板的过程，但是在render中利用createElement创建结构的时候代码可读性较低，较为复杂，此时可以利用jsx语法来在render中创建dom，解决这个问题，但是前提是需要使用工具来编译jsx



## 2. 编写第一个react应用程序

react开发需要引入多个依赖文件：react.js、react-dom.js。

使用 使用create-react-app创建项目。把工程目录下的src目录清空，然后在里面重新创建一个index.js. 写入以下代码:
```
// 从 react 的包当中引入了 React。只要你要写 React.js 组件就必须引入React, 因为react里有一种语法叫JSX，稍后会讲到JSX，要写JSX，就必须引入React
import React from 'react'
// ReactDOM 可以帮助我们把 React 组件渲染到页面上去，没有其它的作用了。它是从 react-dom 中引入的，而不是从 react 引入。
import ReactDOM from 'react-dom'

// ReactDOM里有一个render方法，功能就是把组件渲染并且构造 DOM 树，然后插入到页面上某个特定的元素上
ReactDOM.render(
// 这里就比较奇怪了，它并不是一个字符串，看起来像是纯 HTML 代码写在 JavaScript 代码里面。语法错误吗？这并不是合法的 JavaScript 代码, “在 JavaScript 写的标签的”语法叫 JSX- JavaScript XML。
  <h1>欢迎进入React的世界</h1>,
// 渲染到哪里
  document.getElementById('root')
)
```

## 3. 元素与组件

#### 3.1 元素
如果代码多了之后，不可能一直在render方法里写，所以就需要把里面的代码提出来，定义一个变量，像这样：
```
import React from 'react'
import ReactDOM from 'react-dom'
// 这里感觉又不习惯了？这是在用JSX定义一下react元素
const app = <h1>欢迎进入React的世界</h1>
ReactDOM.render(
  app,
  document.getElementById('root')
)
```
组件是由元素构成的。与浏览器的 DOM 元素不同，React 元素是创建开销极小的普通对象。React DOM 会负责更新 DOM 来与 React 元素保持一致。

#### 3.2 函数式组件
由于元素没有办法传递参数，所以我们就需要把之前定义的变量改为一个方法，让这个方法去return一个元素:
```
import React from 'react'
import ReactDOM from 'react-dom'

// 特别注意这里的写法，如果要在JSX里写js表达式(只能是表达式，不能流程控制)，就需要加 {}，包括注释也是一样，并且可以多层嵌套
const app = (props) => <h1>欢迎进入{props.name}的世界</h1>

ReactDOM.render(
  app({
    name: 'react'
  }),
  document.getElementById('root')
)
```
这里我们定义的方法实际上也是react定义组件的第一种方式-定义函数式组件，这也是无状态组件。但是这种写法不符合react的jsx的风格，更好的方式是使用以下方式进行改造
```
import React from 'react'
import ReactDOM from 'react-dom'

const App = (props) => <h1>欢迎进入{props.name}的世界</h1>

ReactDOM.render(
  // React组件的调用方式
  <App name="react" />,
  document.getElementById('root')
)
```
这样一个完整的函数式组件就定义好了。但要注意！注意！注意！组件名必须大写，否则报错。

#### 3.3 class组件
ES6的加入让JavaScript直接支持使用class来定义一个类，react的第二种创建组件的方式就是使用的类的继承，ES6 class是目前官方推荐的使用方式，它使用了ES6标准语法来构建，看以下代码：
```
import React from 'react'
import ReactDOM from 'react-dom'

class App extends React.Component {
  render () {
    return (
      // 注意这里得用this.props.name, 必须用this.props
      <h1>欢迎进入{this.props.name}的世界</h1>
  	)
  }
}
ReactDOM.render(
  <App name="react" />,
  document.getElementById('root')
)
```
运行结果和之前完全一样，因为JS里没有真正的class，这个class只是一个语法糖, 但二者的底层运行机制不一样。

函数式组件是直接调用, 在前面的代码里已经有看到

es6 class组件其实就是一个构造器,每次使用组件都相当于在实例化组件，像这样：
```
import React from 'react'
import ReactDOM from 'react-dom'

class App extends React.Component {
  render () {
    return (
  		<h1>欢迎进入{this.props.name}的世界</h1>
  	)
  }
}

const app = new App({
  name: 'react'
}).render()

ReactDOM.render(
  app,
  document.getElementById('root')
)
```

#### 3.4 更老的一种方法
在16以前的版本还支持这样创建组件, 但现在的项目基本上不用
```
React.createClass({
  render () {
    return (
      <div>{this.props.xxx}</div>
  	)
  }
})
```

#### 3.5 组件的组合、嵌套
将一个组件渲染到某一个节点里的时候，会将这个节点里原有内容覆盖

组件嵌套的方式就是将子组件写入到父组件的模板中去，且react没有Vue中的内容分发机制（slot），所以我们在一个组件的模板中只能看到父子关系

```
// 从 react 的包当中引入了 React 和 React.js 的组件父类 Component
// 还引入了一个React.js里的一种特殊的组件 Fragment
import React, { Component, Fragment } from 'react'
import ReactDOM from 'react-dom'

class Title extends Component {
  render () {
    return (
      <h1>欢迎进入React的世界</h1>
  	)
  }
}
class Content extends Component {
  render () {
    return (
      <p>React.js是一个构建UI的库</p>
  	)
  }
}
/** 由于每个React组件只能有一个根节点，所以要渲染多个组件的时候，需要在最外层包一个容器，如果使用div, 会生成多余的一层dom
class App extends Component {
  render () {
    return (
    	<div>
    		<Title />
        <Content />
      </div>
  	)
  }
}
**/
// 如果不想生成多余的一层dom可以使用React提供的Fragment组件在最外层进行包裹
class App extends Component {
  render () {
    return (
      <Fragment>
      	<Title />
        <Content />
      </Fragment>
  	)
  }
}
ReactDOM.render(
  <App/>,
  document.getElementById('root')
)
```

## 4. JSX 原理
要明白JSX的原理，需要先明白如何用 JavaScript 对象来表现一个 DOM 元素的结构.

看下面的DOM结构
```
<div class='app' id='appRoot'>
  <h1 class='title'>欢迎进入React的世界</h1>
  <p>
    React.js 是一个帮助你构建页面 UI 的库
  </p>
</div>
```
上面这个 HTML 所有的信息我们都可以用 JavaScript 对象来表示：
```
{
  tag: 'div',
  attrs: { className: 'app', id: 'appRoot'},
  children: [
    {
      tag: 'h1',
      attrs: { className: 'title' },
      children: ['欢迎进入React的世界']
    },
    {
      tag: 'p',
      attrs: null,
      children: ['React.js 是一个构建页面 UI 的库']
    }
  ]
}
```
但是用 JavaScript 写起来太长了，结构看起来又不清晰，用 HTML 的方式写起来就方便很多了。

于是 React.js 就把 JavaScript 的语法扩展了一下，让 JavaScript 语言能够支持这种直接在 JavaScript 代码里面编写类似 HTML 标签结构的语法，这样写起来就方便很多了。编译的过程会把类似 HTML 的 JSX 结构转换成 JavaScript 的对象结构。

下面代码:
```
import React from 'react'
import ReactDOM from 'react-dom'

class App extends React.Component {
  render () {
    return (
      <div className='app' id='appRoot'>
        <h1 className='title'>欢迎进入React的世界</h1>
        <p>
          React.js 是一个构建页面 UI 的库
        </p>
      </div>
    )
  }
}

ReactDOM.render(
	<App />,
  document.getElementById('root')
)
```
编译之后将得到这样的代码:
```
import React from 'react'
import ReactDOM from 'react-dom'

class App extends React.Component {
  render () {
    return (
      React.createElement(
        "div",
        {
          className: 'app',
          id: 'appRoot'
        },
        React.createElement(
          "h1",
          { className: 'title' },
          "欢迎进入React的世界"
        ),
        React.createElement(
          "p",
          null,
          "React.js 是一个构建页面 UI 的库"
        )
      )
    )
  }
}

ReactDOM.render(
	React.createElement(App),
  document.getElementById('root')
)
```
React.createElement 会构建一个 JavaScript 对象来描述你 HTML 结构的信息，包括标签名、属性、还有子元素等, 语法为
```
React.createElement(
  type,
  [props],
  [...children]
)
```
所谓的 JSX 其实就是 JavaScript 对象，所以使用 React 和 JSX 的时候一定要经过编译的过程:

    JSX —使用react构造组件，bable进行编译—> JavaScript对象 — ReactDOM.render()—>DOM元素 —>插入页面

## 5. 组件中DOM样式

#### 5.1 行内样式
想给虚拟dom添加行内样式，需要使用表达式传入样式对象的方式来实现：
```
// 注意这里的两个括号，第一个表示我们在要JSX里插入JS了，第二个是对象的括号
 <p style={{color:'red', fontSize:'14px'}}>Hello world</p>
```
行内样式需要写入一个样式对象，而这个样式对象的位置可以放在很多地方，例如render函数里、组件原型上、外链js文件中

#### 5.2 使用class
React推荐我们使用行内样式，因为React觉得每一个组件都是一个独立的整体

其实我们大多数情况下还是大量的在为元素添加类名，但是需要注意的是，class需要写成className（因为毕竟是在写类js代码，会受到js规则的限制，而class是关键字）
```
<p className="hello" style = {this.style}>Hello world</p>
```

#### 5.3 不同的条件添加不同的样式
有时候需要根据不同的条件添加不同的样式，比如：完成状态，完成是绿色，未完成是红色。
```
 <div className={over ? "green":"red"}></div>
```
#### 5.4 classnames(自学)
而对于有多个类的情况下推荐使用[classnames](https://www.npmjs.com/package/classnames)这个包：https://www.npmjs.com/package/classnames
示例：
```
var classNames = require('classnames');
 
var Button = React.createClass({
  // ...
  render () {
    var btnClass = classNames({
      btn: true, //true，表示添加这个类
      'btn-pressed': this.state.isPressed,//根据isPressed 判断是否添加  btn-pressed
      'btn-over': this.state.isHovered //isHovered 判断是否添加 btn-over
    });
    return <button className={btnClass}>{this.props.label}</button>;
  }
});
```

#### 5.5 css-in-js (自学)
styled-components是针对React写的一套css-in-js框架，简单来讲就是在js中写css。[npm链接](https://www.npmjs.com/package/styled-components)
示例：
```
import React from 'react'
import ReactDOM from 'react-dom'

import styled from 'styled-components';
 
//创建一个 渲染成h1的<Title>组件,并设置它的样式
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;
 
//创建一个渲染成section的<Wrapper>组件,并设置它的样式
const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;
`;
 
//使用这些组件
const App =  () => {
    return  (
        <Wrapper>
        <Title>Hello World, this is my first styled component!</Title>
      </Wrapper>
      )
}

ReactDOM.render(
	<App />,
  document.getElementById('root')
)
```

## 6. 组件的数据挂载方式

#### 6.1 属性(props)

props是正常是外部传入的，组件内部也可以通过一些方式来初始化的设置，属性不能被组件自己更改，但是你可以通过父组件主动重新渲染的方式来传入新的 props

属性是描述性质、特点的，组件自己不能随意更改。

之前的组件代码里面有props的简单使用，总的来说，在使用一个组件的时候，可以把参数放在标签的属性当中，所有的属性都会作为组件 props 对象的键值。通过箭头函数创建的组件，需要通过函数的参数来接收props:

```
import React, { Component, Fragment } from 'react'
import ReactDOM from 'react-dom'

class Title extends Component {
  render () {
    return (
  		<h1>欢迎进入{this.props.name}的世界</h1>
  	)
  }
}

const Content = (props) => {
  return (
    <p>{props.name}是一个构建UI的库</p>
  )
}

class App extends Component {
  render () {
    return (
  		<Fragment>
      	<Title name="React" />
        <Content name="React.js" />
      </Fragment>
  	)
  }
}

ReactDOM.render(
	<App/>,
  document.getElementById('root')
)
```
#### 6.2 设置组件的默认props
```
import React, { Component, Fragment } from 'react'
import ReactDOM from 'react-dom'

class Title extends Component {
  // 使用类创建的组件，直接在这里写static方法，创建defaultProps
  static defaultProps = {
    name: 'React'
  }
  render () {
    return (
  		<h1>欢迎进入{this.props.name}的世界</h1>
  	)
  }
}

const Content = (props) => {
  return (
    <p>{props.name}是一个构建UI的库</p>
  )
}

// 使用箭头函数创建的组件，需要在这个组件上直接写defaultProps属性
Content.defaultProps = {
  name: 'React.js'
}

class App extends Component {
  render () {
    return (
  		<Fragment>
        {/* 由于设置了defaultProps， 不传props也能正常运行，如果传递了就会覆盖defaultProps的值 */}
      	<Title />
        <Content />
      </Fragment>
  	)
  }
}

ReactDOM.render(
	<App/>,
  document.getElementById('root')
)
```

#### 6.3 props.children

我们知道使用组件的时候，可以嵌套。要在自定义组件的使用嵌套结构，就需要使用 props.children 。在实际的工作当中，我们几乎每天都需要用这种方式来编写组件。

```
import React, { Component, Fragment } from 'react'
import ReactDOM from 'react-dom'

class Title extends Component {
  render () {
    return (
  		<h1>欢迎进入{this.props.children}的世界</h1>
  	)
  }
}

const Content = (props) => {
  return (
    <p>{props.children}</p>
  )
}

class App extends Component {
  render () {
    return (
  		<Fragment>
      	<Title>React</Title>
        <Content><i>React.js</i>是一个构建UI的库</Content>
      </Fragment>
  	)
  }
}

ReactDOM.render(
	<App/>,
  document.getElementById('root')
)
```

#### 6.4 使用prop-types检查props

React其实是为了构建大型应用程序而生, 在一个大型应用中，根本不知道别人使用你写的组件的时候会传入什么样的参数，有可能会造成应用程序运行不了，但是不报错。为了解决这个问题，React提供了一种机制，让写组件的人可以给组件的props设定参数检查，需要安装和使用[prop-types](https://www.npmjs.com/package/prop-types):https://www.npmjs.com/package/prop-types
```
npm i prop-types -S
```

```
import PropTypes from 'prop-types';

class MyComponent extends React.Component {
  render() {
    // ... do things with the props
  }
} 
 
 
MyComponent.propTypes = {
    name: PropTypes.string,
    age: PropTypes.number
}
```

#### 6.5 状态(state)

状态就是组件描述某种显示情况的数据，由组件自己设置和更改，也就是说由组件自己维护，使用状态的目的就是为了在不同的状态下使组件的显示不同(自己管理)

定义state

第一种方式
```
import React, { Component } from 'react'
import ReactDOM from 'react-dom'

class App extends Component {
  state = {
    name: 'React',
    isLiked: false
  }
  render () {
    return (
      <div>
        <h1>欢迎来到{this.state.name}的世界</h1>
        <button>
          {
            this.state.isLiked ? '❤️取消' : '🖤收藏'
          }
        </button>
      </div>
  	)
  }
}
ReactDOM.render(
	<App/>,
  document.getElementById('root')
)
```
另一种方式(推荐)
```
import React, { Component } from 'react'
import ReactDOM from 'react-dom'

class App extends Component {
  constructor() {
    super()
    this.state = {
      name: 'React',
      isLiked: false
    }
  }
  render () {
    return (
  		<div>
        <h1>欢迎来到{this.state.name}的世界</h1>
        <button>
          {
            this.state.isLiked ? '❤️取消' : '🖤收藏'
          }
        </button>
      </div>
  	)
  }
}
ReactDOM.render(
  <App/>,
  document.getElementById('root')
)
```
this.props和this.state是纯js对象,在vue中，data属性是利用Object.defineProperty处理过的，更改​data的数据的时候会触发数据的getter和setter，但是React中没有做这样的处理，如果直接更改的话，react是无法得知的，所以，需要使用特殊的更改状态的方法setState。

#### 6.6 setState
isLiked 存放在实例的 state 对象当中，组件的 render 函数内，会根据组件的 state 的中的isLiked不同显示“取消”或“收藏”内容。下面给 button 加上了点击的事件监听。
```
import React, { Component } from 'react'
import ReactDOM from 'react-dom'

class App extends Component {
  constructor() {
    super()
    this.state = {
      name: 'React',
      isLiked: false
    }
  }
  handleBtnClick = () => {
    this.setState({
      isLiked: !this.state.isLiked
    })
  }
  render () {
    return (
      <div>
        <h1>欢迎来到{this.state.name}的世界</h1>
        <button onClick={this.handleBtnClick}>
          {
            this.state.isLiked ? '❤️取消' : '🖤收藏'
          }
        </button>
      </div>
  	)
  }
}
ReactDOM.render(
	<App/>,
  document.getElementById('root')
)
```
setState有两个参数

第一个参数可以是对象，也可以是方法return一个对象，我们把这个参数叫做updater

* 参数是对象
```
this.setState({
  isLiked: !this.state.isLiked
})
```
* 参数是方法
```
this.setState((prevState, props) => {
  return {
    isLiked: !prevState.isLiked
  }
})
```
==注意的是这个方法接收两个参数，第一个是上一次的state, 第二个是props==

setState是异步的，所以想要获取到最新的state，没有办法获取，就有了第二个参数，这是一个可选的回调函数
```
this.setState((prevState, props) => {
  return {
    isLiked: !prevState.isLiked
  }
}, () => {
  console.log('回调里的',this.state.isLiked)
})
console.log('setState外部的',this.state.isLiked)
```

#### 6.7 属性vs状态

相似点：都是纯js对象，都会触发render更新，都具有确定性（状态/属性相同，结果相同）

不同点：

1. 属性能从父组件获取，状态不能
2. 属性可以由父组件修改，状态不能
3. 属性能在内部设置默认值，状态也可以
4. 属性不在组件内部修改，状态要改
5. 属性能设置子组件初始值，状态不可以
6. 属性可以修改子组件的值，状态不可以

state 的主要作用是用于组件保存、控制、修改自己的可变状态。state 在组件内部初始化，可以被组件自身修改，而外部不能访问也不能修改。你可以认为 state 是一个局部的、只能被组件自身控制的数据源。state 中状态可以通过 this.setState方法进行更新，setState 会导致组件的重新渲染。

props 的主要作用是让使用该组件的父组件可以传入参数来配置该组件。它是外部传进来的配置参数，组件内部无法控制也无法修改。除非外部组件主动传入新的 props，否则组件的 props 永远保持不变。

如果搞不清 state 和 props 的使用场景，记住一个简单的规则：尽量少地用 state，多用 props。

没有 state 的组件叫无状态组件（stateless component），设置了 state 的叫做有状态组件（stateful component）。因为状态会带来管理的复杂性，我们尽量多地写无状态组件，尽量少地写有状态的组件。这样会降低代码维护的难度，也会在一定程度上增强组件的可复用性。

#### 6.8 状态提升

如果有多个组件共享一个数据，把这个数据放到共同的父级组件中来管理


## 7. 受控组件与非受控组件
React组件的数据渲染是否被调用者传递的props完全控制，控制则为受控组件，否则非受控组件。

表单：https://zh-hans.reactjs.org/docs/forms.html
## 8. 渲染数据
#### 8.1 条件渲染
```
{
  condition ? '❤️取消' : '🖤收藏'
}
```
#### 8.2 列表渲染
```
// 数据
const people = [{
  id: 1,
  name: 'Leo',
  age: 35
}, {
  id: 2,
  name: 'XiaoMing',
  age: 16
}]
// 渲染列表
{
  people.map(person => {
    return (
      <dl key={person.id}>
        <dt>{person.name}</dt>
        <dd>age: {person.age}</dd>
      </dl>
    )
  })
}
```

React的高效依赖于所谓的 Virtual-DOM，尽量不碰 DOM。对于列表元素来说会有一个问题：元素可能会在一个列表中改变位置。要实现这个操作，只需要交换一下 DOM 位置就行了，但是React并不知道其实我们只是改变了元素的位置，所以它会重新渲染后面两个元素（再执行 Virtual-DOM ），这样会大大增加 DOM 操作。但如果给每个元素加上唯一的标识，React 就可以知道这两个元素只是交换了位置，这个标识就是key，这个 key 必须是每个元素唯一的标识

#### 8.3 dangerouslySetHTML
对于富文本创建的内容，后台拿到的数据是这样的：
```
content = "<p>React.js是一个构建UI的库</p>"
```
处于安全的原因，React当中所有表达式的内容会被转义，如果直接输入，标签会被当成文本。这时候就需要使用dangerouslySetHTML属性，它允许我们动态设置innerHTML
```
import React, { Component } from 'react'
import ReactDOM from 'react-dom'

class App extends Component {
  constructor() {
    super()
    this.state = {
      content : "<p>React.js是一个构建UI的库</p>"
    }
  }
  render () {
    return (
  		<div
        // 注意这里是两个下下划线 __html
        dangerouslySetInnerHTML={{__html: this.state.content}}
      />
  	)
  }
}
ReactDOM.render(
	<App/>,
  document.getElementById('root')
)
```


## 9. 事件处理
#### 9.1 绑定事件
采用on+事件名的方式来绑定一个事件，注意，这里和原生的事件是有区别的，原生的事件全是小写onclick, React里的事件是驼峰onClick，React的事件并不是原生事件，而是合成事件。

#### 9.2 事件handler的写法
* 直接在render里写行内的箭头函数(不推荐)
* 在组件内使用箭头函数定义一个方法(推荐)
* 直接在组件内定义一个非箭头函数的方法，然后在render里直接使用onClick={this.handleClick.bind(this)}(不推荐)
* 直接在组件内定义一个非箭头函数的方法，然后在constructor里bind(this)(推荐)

#### 9.3 Event 对象
和普通浏览器一样，事件handler会被自动传入一个 event 对象，这个对象和普通的浏览器 event 对象所包含的方法和属性都基本一致。不同的是 React中的 event 对象并不是浏览器提供的，而是它自己内部所构建的。它同样具有event.stopPropagation、event.preventDefault 这种常用的方法

#### 9.4 事件的参数传递
* 在render里调用方法的地方外面包一层箭头函数
* 在render里通过this.handleEvent.bind(this, 参数)这样的方式来传递

#### 9.5 处理用户输入
```
import React, { Component } from 'react'
import ReactDOM from 'react-dom'

class App extends Component {
  constructor() {
    super()
    this.state = {
      msg: ''
    }
  }
  handleInputChange = (e) => {
    this.setState({
      msg: e.target.value
    })
  }

  render () {

    return (
  		<div>
        <label>
          <input
            type="text"
            value={this.state.msg}
            onChange={this.handleInputChange}
          />
        </label>
        <h1>{this.state.msg}</h1>
      </div>
  	)
  }
}
ReactDOM.render(
	<App/>,
  document.getElementById('root')
)
```


## 10. 学员练习所学内容并将 todolist 案例完善

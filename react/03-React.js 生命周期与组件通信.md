## 1. 组件的生命周期
>React中组件也有生命周期，也就是说也有很多钩子函数供我们使用, 组件的生命周期，我们会分为四个阶段，初始化、运行中、销毁、错误处理(16.3之后)

#### 1.1 初始化
在组件初始化阶段会执行

* `constructor` 💎💎💎💎
* `static getDerivedStateFromProps()` 💎💎
* `componentWillMount()` / `UNSAFE_componentWillMount()`
* `render()`  💎💎💎💎
* `componentDidMount()`  💎💎💎💎

#### 1.2 更新阶段
props或state的改变可能会引起组件的更新，组件重新渲染的过程中会调用以下方法：

* `componentWillReceiveProps()` / `UNSAFE_componentWillReceiveProps()`
* `static getDerivedStateFromProps()`
* `shouldComponentUpdate()` 💎💎💎
* `componentWillUpdate()` / `UNSAFE_componentWillUpdate()`
* `render()`
* `getSnapshotBeforeUpdate()`
* `componentDidUpdate()` 💎💎

#### 1.3 卸载阶段
`componentWillUnmount()` 💎💎

#### 1.4 错误处理
`componentDidCatch()`
![image](http://note.youdao.com/yws/res/8739/B848F849070942EF9B5CBEEE72CBD860)

#### 1.5 各生命周期详解

##### 1. constructor(props)

React组件的构造函数在挂载之前被调用。在实现React.Component构造函数时，需要先在添加其他内容前，调用super(props)，用来将父组件传来的props绑定到这个类中，使用this.props将会得到。

官方建议不要在`constructor`引入任何具有副作用和订阅功能的代码，这些应当使用`componentDidMount()`。

`constructor`中应当做些初始化的动作，如：初始化state，将事件处理函数绑定到类实例上，但也不要使用setState()。如果没有必要初始化state或绑定方法，则不需要构造`constructor`，或者把这个组件换成纯函数写法。

当然也可以利用props初始化state，在之后修改state不会对props造成任何修改，但仍然建议大家提升状态到父组件中，或使用redux统一进行状态管理。
```
constructor(props) {
  super(props);
  this.state = {
    isLiked: props.isLiked
  };
}
```

##### 2.static getDerivedStateFromProps(nextProps, prevState)

`getDerivedStateFromProps` 是react16.3之后新增，在组件实例化后，和接受新的props后被调用。他必须返回一个对象来更新状态，或者返回null表示新的props不需要任何state的更新。

如果是由于父组件的props更改，所带来的重新渲染，也会触发此方法。

调用`steState()`不会触发`getDerivedStateFromProps()`。

之前这里都是使用`constructor+componentWillRecieveProps`完成相同的功能的

##### 3. componentWillMount() / UNSAFE_componentWillMount()
`componentWillMount()`将在React未来版本(官方说法 17.0)中被弃用。
`UNSAFE_componentWillMount()`在组件挂载前被调用，在这个方法中调用setState()不会起作用，是由于他在render()前被调用。

为了避免副作用和其他的订阅，官方都建议使用`componentDidMount()`代替。这个方法是用于在服务器渲染上的唯一方法。这个方法因为是在渲染之前被调用，也是惟一一个可以直接同步修改state的地方。

##### 4.render()
render()方法是必需的。当他被调用时，他将计算this.props和this.state，并返回以下一种类型：

* React元素。通过jsx创建，既可以是dom元素，也可以是用户自定义的组件。
* 字符串或数字。他们将会以文本节点形式渲染到dom中。
* Portals。react 16版本中提出的新的解决方案，可以使组件脱离父组件层级直接挂载在DOM树的任何位置。
* null，什么也不渲染
* 布尔值。也是什么都不渲染。
当返回null,false,ReactDOM.findDOMNode(this)将会返回null，什么都不会渲染。

render()方法必须是一个纯函数，他不应该改变state，也不能直接和浏览器进行交互，应该将事件放在其他生命周期函数中。 如果`shouldComponentUpdate()`返回false，render()不会被调用。

##### 5. componentDidMount
componentDidMount在组件被装配后立即调用。初始化使得DOM节点应该进行到这里。

通常在这里进行ajax请求

如果要初始化第三方的dom库，也在这里进行初始化。只有到这里才能获取到真实的dom.

##### 6.componentWillReceiveProps()/UNSAFE_componentWillReceiveProps(nextProps)
官方建议使用`getDerivedStateFromProps`函数代替`componentWillReceiveProps`。当组件挂载后，接收到新的props后会被调用。如果需要更新state来响应props的更改，则可以进行this.props和nextProps的比较，并在此方法中使用this.setState()。

如果父组件会让这个组件重新渲染，即使props没有改变，也会调用这个方法。

React不会在组件初始化props时调用这个方法。调用this.setState也不会触发。

##### 7.shouldComponentUpdate(nextProps, nextState)
调用`shouldComponentUpdate`使React知道，组件的输出是否受state和props的影响。默认每个状态的更改都会重新渲染，大多数情况下应该保持这个默认行为。

在渲染新的props或state前，`shouldComponentUpdate`会被调用。默认为true。这个方法不会在初始化时被调用，也不会在forceUpdate()时被调用。返回false不会阻止子组件在state更改时重新渲染。

如果`shouldComponentUpdate()`返回false，`componentWillUpdate`,`render`和`componentDidUpdate`不会被调用。

官方并不建议在`shouldComponentUpdate()`中进行深度查询或使用JSON.stringify()，他效率非常低，并且损伤性能。

##### 8.UNSAFE_componentWillUpdate(nextProps, nextState)
在渲染新的state或props时，`UNSAFE_componentWillUpdate`会被调用，将此作为在更新发生之前进行准备的机会。这个方法不会在初始化时被调用。

不能在这里使用`this.setState()`，也不能做会触发视图更新的操作。如果需要更新state或props，调用`getDerivedStateFromProps`。

##### 9.getSnapshotBeforeUpdate()
在react render()后的输出被渲染到DOM之前被调用。它使您的组件能够在它们被潜在更改之前捕获当前值（如滚动位置）。这个生命周期返回的任何值都将作为参数传递给`componentDidUpdate()`。

##### 10.componentDidUpdate(prevProps, prevState, snapshot)
在更新发生后立即调用`componentDidUpdate()`。此方法不用于初始渲染。当组件更新时，将此作为一个机会来操作DOM。只要您将当前的props与以前的props进行比较（例如，如果props没有改变，则可能不需要网络请求），这也是做网络请求的好地方。

如果组件实现`getSnapshotBeforeUpdate()`生命周期，则它返回的值将作为第三个“快照”参数传递给`componentDidUpdate()`。否则，这个参数是undefined。

##### 11.componentWillUnmount()
在组件被卸载并销毁之前立即被调用。在此方法中执行任何必要的清理，例如使定时器无效，取消网络请求或清理在`componentDidMount`中创建的任何监听。

##### 12.componentDidCatch(error, info)
错误边界是React组件，可以在其子组件树中的任何位置捕获JavaScript错误，记录这些错误并显示回退UI，而不是崩溃的组件树。错误边界在渲染期间，生命周期方法以及整个树下的构造函数中捕获错误。

如果类组件定义了此生命周期方法，则它将成错误边界。在它中调用setState()可以让你在下面的树中捕获未处理的JavaScript错误，并显示一个后备UI。只能使用错误边界从意外异常中恢复; 不要试图将它们用于控制流程。

错误边界只会捕获树中下面组件中的错误。错误边界本身不能捕获错误。
#### 1.6 演示示例：
```
//App.js
import React,{Component} from 'react';

import LifeCycle from './LifeCycle';

export default class extends Component{
    render(){
        return (
            <div>
                <LifeCycle/>
            </div>
        )
    }
}
```

```
//LifeCycle.js
import React,{Component} from 'react';

import LifeSon from './LifeSon';

export default class extends Component{

    constructor(props){
        super(props);
        console.log('constructor....')
        this.state = {
            title:0
        }
    }
    // static getDerivedStateFromProps(nextProps, prevState){
    //     console.log('getDerivedStateFromProps...')
    //     return null;
    // }

    UNSAFE_componentWillMount(){
        console.log('UNSAFE_componentWillMount...')
    }

    updateTitle = () =>{
        this.setState({
            title:this.state.title + 1
        })
    }
    render(){
        // console.log('render....')

        return (<div>
                <h1>hello 1903</h1>
                <LifeSon title={this.state.title} />

                <button onClick={this.updateTitle}> update </button>
            </div>)
    }

    componentDidMount(){
        console.log('componentDidMount....')
    }
}
```
```
//LifeSon.js
import React,{Component} from 'react';

export default class extends Component{

    constructor(props){
        super(props);
    }
    UNSAFE_componentWillReceiveProps(nextProps){
        console.log('UNSAFE_componentWillReceiveProps',nextProps)
    }

    // static getDerivedStateFromProps(nextProps, prevState){
    //     console.log('getDerivedStateFromProps...')
    //     return null;
    // }

    shouldComponentUpdate(nextProps, nextState){
        console.log('shouldComponentUpdate',nextProps,nextState)

        if(nextProps.title %2 === 0){
            return true;
        }else{
            return false
        }
    }

    UNSAFE_componentWillUpdate(nextProps, nextState){
        console.log('UNSAFE_componentWillUpdate',nextProps,nextState)
    }

    render(){
        console.log('render');
        return(<div>
                <h1>life son  {this.props.title} </h1>
                </div>)
    }

    getSnapshotBeforeUpdate(){
        console.log('getSnapshotBeforeUpdate');
        return 100;
    }

    componentDidUpdate(prevProps, prevState, snapshot){
        console.log('getSnapshotBeforeUpdate',prevProps,prevState,snapshot);
    }

}
```

#### 1.7 请求数据 💎💎💎💎
1. 安装axios `npm i axios -S`
2. 在`componentDidMount`生命周期函数里调用接口，获取数据
```
import React,{Component} from 'react';
import axios from 'axios';
import './book.css';

export default class extends Component{
    constructor(props){
        super(props);

        this.state = {
            books:[],
            loading:false
        }
    }

    // 在这个生命周期函数里请求接口
    componentDidMount(){
        // 请求开始前，设置loading为true，显示loading
        this.setState({
            loading:true
        })
        axios.get('https://www.easy-mock.com/mock/5d1c66e9e85bc1461e567f67/api/books')
        .then(res =>{
            console.log(res)
            this.setState({
                books:res.data.collections, //把请求成功的数据保存到state中
                loading:false  // 把loading设置为false，影藏loading
            })
        })
    }

    render(){
        // 通过map函数，把数据数组转换成 组件数组
        let books = this.state.books;
        let bookComp = books.map( book => 
            (<li  key={book.id} className="book">
                <div>{book.title}</div>
                <div>{book.recommend_comment}</div>
            </li> )
        )
        
        // 如果loading为true，则显示正在加载，否则显示主页内容
        if(this.state.loading){
            return <div>正在加载。。。。</div>
        }else{
            return (
                <div>
                    <ul>
                        {bookComp}
                    </ul>
                </div>
            )
        }
    }
}
```




## 2. PureComponent 💎💎
PureComponnet里如果接收到的新属性或者是更改后的状态和原属性、原状态相同的话，就不会去重新render了 在里面也可以使用`shouldComponentUpdate`，而且。是否重新渲染以`shouldComponentUpdate`的返回值为最终的决定因素。
```
import React, { PureComponent } from 'react'

class YourComponent extends PureComponent {
  ……
}
```
#### 示例代码：
```
//父组件
export default class extends Component{

    constructor(){
        super()
        this.state = {
            count:0
        }
    }
    update = () =>{
        this.setState({
            count: this.state.count 
        })
    } 
    render() {
        return(<div>
                 <MyPur count={this.state.count}/>
                 <button onClick={this.update}>update</button>
               </div>)   
    }
}
```
```
//子组件，是个PureComponent
import React, { PureComponent,Component } from 'react'

export default class YourComponent extends PureComponent {

    shouldComponentUpdate(props){
        console.log(props)
        return true;
    }
    render(){
        console.log('render...')
        return (<div>
                {this.props.count}
            </div>)
    }
}
```


## 3. ref 💎💎💎💎
React提供的这个ref属性，表示为对组件真正实例的引用，其实就是ReactDOM.render()返回的组件实例,ref可以挂载到组件上也可以是dom元素上。

* 挂到组件(class声明的组件)上的ref表示对组件实例的引用。不能在函数式组件上使用 ref 属性，因为它们没有实例：
* 挂载到dom元素上时表示具体的dom元素节点。
在React 最新的版本中，要使用ref, 需要使用React.createRef方法先生成一个ref。
```
import React, { Component, createRef } from 'react'
import ReactDOM from 'react-dom'

class App extends Component {
  constructor() {
    super()
    // 创建inputRef
    this.inputRef=createRef()
  }
  componentDidMount () {
    console.log(this.inputRef.current) // <input type="text">
  }
  render () {
    return (
  		<div>
        {/* 关联ref和dom */}
        <input type="text" ref={this.inputRef} />
      </div>
  	)
  }
}
ReactDOM.render(
	<App/>,
  document.getElementById('root')
)
```

## 4. React Hooks 
React Hooks 是 React 16.7.0-alpha 版本推出的新特性, 有了React Hooks，在 react 函数组件中，也可以使用类组件（classes components）的 state 和 组件生命周期。通过下面几个例子来学习React Hooks。

#### 4.1 State Hook 💎💎💎
```
// useState是react包提供的一个方法
import React, { useState } from "react";
import ReactDOM from "react-dom";

const Counter = () => {
  // useState 这个方法可以为我们的函数组件拥有自己的state，它接收一个用于初始 state 的值，返回一对变量。这里我们把计数器的初始值设置为0, 方法都是以set开始
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>你点击了{count}次</p>
      <button onClick={() => setCount(count + 1)}>点击</button>
    </div>
  );
};

const rootElement = document.getElementById("root");

ReactDOM.render(<Counter />, rootElement);
```

#### 4.2 Effect Hook 💎💎💎
```
// useState是react包提供的一个方法
import React, { useState, useEffect } from "react";
import ReactDOM from "react-dom";

const Counter = () => {
  // useState 这个方法可以为我们的函数组件拥有自己的state，它接收一个用于初始 state 的值，返回一对变量。这里我们把计数器的初始值设置为0, 方法都是以set开始
  const [count, setCount] = useState(0);
  // 类似于componentDidMount或者componentDidUpdate:
  useEffect(() => {
    // 更改网页的标题，还可以做其它的监听
    document.title = `你点击了${count}次`;
  });
  return (
    <div>
      <p>你点击了{count}次</p>
      <button onClick={() => setCount(count + 1)}>点击</button>
    </div>
  );
};

const rootElement = document.getElementById("root");

ReactDOM.render(<Counter />, rootElement);
```
##### 示例代码：
```
import React,{useState,useEffect} from  'react';

import axios from 'axios';

const Series = () =>{
    // 使用 State Hook
    let [series,updateSeries] = useState([]);

    // 使用Effect Hook，在此请求数据
    useEffect(()=>{
        axios.get('https://www.easy-mock.com/mock/5d1c66e9e85bc1461e567f67/api/series')
        .then(res=>{
            console.log(res)
            updateSeries(res.data.collections)
        })
    })

    return <div>
            <ul>
            {series.map( se =>{
                return <li ket={se.id}>{se.title}</li>
            })}
            </ul>
        </div>
}

export default Series;
```


#### 4.3 React Hooks 的规则
* 只能在顶层调用Hooks。不要在循环，条件或嵌套函数中调用Hook。
* 不要从常规JavaScript函数中调用Hook。只在React函数式组件调用Hooks。

#### 4.4 react 内置hooks api
* Basic Hooks
    - useState
    - useEffect
    - useContext
* Additional Hooks
    - useReducer
    - useCallback
    - useMemo
    - useRef
    - useImperativeHandle
    - useLayoutEffect
    - useDebugValue


## 5. 组件通信

#### 5.1 父组件与子组件通信 💎💎💎💎

* 父组件将自己的状态传递给子组件，子组件当做属性来接收，当父组件更改自己状态的时候，子组件接收到的属性就会发生改变
* 父组件利用ref对子组件做标记，通过调用子组件的方法以更改子组件的状态,也可以调用子组件的方法..

#### 5.2 子组件与父组件通信 💎💎💎

* 父组件将自己的某个方法传递给子组件，在方法里可以做任意操作，比如可以更改状态，子组件通过this.props接收到父组件的方法后调用。

#### 5.3 跨组件通信 💎💎💎

> 在react没有类似vue中的事件总线来解决这个问题，我们只能借助它们共同的父级组件来实现，将非父子关系装换成多维度的父子关系。        
react提供了context api来实现跨组件通信, React 16.3之后的context api较之前的好用。


##### context api:
* React.createContext()
* Provider
* Consumer


##### React.createContext()
> 这个方法用来创建context对象，并包含Provider、Consumer两个组件 <Provider />、<Consumer />

示例:  
```
const {Provider, Consumer} = React.createContext();
```


##### Provider
>数据的生产者，通过value属性接收存储的公共状态，来传递给子组件或后代组件  

示例:   
```
<Provider value={/* some value */}>
```

##### Consumer
>数据的消费者，通过订阅Provider传入的context的值，来实时更新当前组件的状态

示例： 
```
<Consumer>
  {value => /* render something based on the context value */}
</Consumer>
```

==每当Provider的值发生改变时, 作为Provider后代的所有Consumers都会重新渲染==
![prop vs context](http://note.youdao.com/yws/res/8784/9DB1C98739DB40C49523605441864505)


实例，使用context 实现购物车中的加减功能
```
import React,{Component,createContext} from 'react';

const {Provider,Consumer} = createContext();

// 定义一个容器组件，内部使用Provider封装，提供数据
class Container extends Component{

    constructor(){
        super()
        this.state = {
            number:0
        }
        this.increase = this.increase.bind(this);
        this.decrease = this.decrease.bind(this);
    }
    increase(){
        this.setState({
            number:this.state.number + 1
        })
    }

    decrease(){
        this.setState({
            number:this.state.number - 1
        })
    }

    render(){
        return (
            <Provider value={{
                number:this.state.number,
                increase:this.increase,
                decrease:this.decrease
            }}>
            {this.props.children}
            </Provider>
        )
    }
}
// 显示数据的组件
const NumberLabel = () =>{
    return (<Consumer>
            { ({number})=>{
                return <div>{number}</div>
            }}
        </Consumer>)
}

// const AddBtn = () =>{
//     return (<Consumer>
//         {({increase})=>{
//             return <button onClick={increase}> + </button>
//         }}
//         </Consumer>)
// }

// const MinBtn = () =>{
//     return (<Consumer>
//         {({decrease})=>{
//             return <button onClick={decrease}> - </button>
//         }}
//         </Consumer>)
// }

// 修改数据的按钮，使用<Consumer>组件封装，内部就可以获取 Provider上的数据
const  NumberBtn = ({type}) =>{
    return (<Consumer>
        
        {({increase,decrease})=>{

            return <button 
                    onClick={type === 'inc' ? increase : decrease}>
                    {type === "inc" ? "+" : "-"}
                    </button>

        }}
        </Consumer>)
}

export  default class extends Component{

    render(){
        return (
            <Container>
                <NumberBtn type="dec"/>
                <NumberLabel/>
                <NumberBtn type="inc"/>
            </Container>
        )
    }
}
```

    复杂的非父子组件通信在react中很难处理，多组件间的数据共享也不好处理，在实际的工作中我们会使用flux、redux、mobx来实现

## 6. HOC(高阶组件)
Higher-Order Components就是一个函数，传给它一个组件，它返回一个新的组件。
```
const NewComponent = higherOrderComponent(YourComponent)
```
比如，我们想要我们的组件通过自动注入一个版权信息。
```
import React,{Component} from 'react';


const copyRight = (ComponentReadyToWrapp) => {

    return () =>{
        return (<div>
                <ComponentReadyToWrapp/>
                <h6> 作者：Davie </h6>
                <h6> 邮箱：iscooleye@163.com </h6>
            </div>)
    }
}

const Book = () =>{
    return <h1> Vue从入门到放弃 </h1>
}

const BookJZB = () =>{
    return <h2> 编程从入门到颈椎病治疗 </h2>
}

// 函数式编程
// let Book1 = copyRight(Book)("davie","is@11.com");
let Book1 = copyRight(Book);
let Book2 = copyRight(BookJZB);


const App = () =>{
    return (
        <div>
            <Book1/>
            <Book2/>
        </div>
    )
}

export default App;
```
这样只要我们有需要用到版权信息的组件，都可以直接使用withCopyright这个高阶组件包裹即可。

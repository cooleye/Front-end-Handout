## vuex 状态管理

## 1. vue-better-scroll  💎💎💎💎
github:https://github.com/huangjincq/vue-better-scroll
1. 安装：
```
npm install vue2-better-scroll -s
```
2. 引入

```
import VueBetterScroll from 'vue2-better-scroll'
```
在组件上注册：
```
export default {
  components: {
    VueBetterScroll
  }
}
```
使用实例：
```
<template>
  <div id="app">
    <header>vue-better-scroll demo</header>
    <main class="position-box">  <!-- 需要一个创建一个父容器 组件高度默认等于父容器的高度 -->
      <vue-better-scroll class="wrapper"
                         ref="scroll"
                         :scrollbar="scrollbarObj"
                         :pullDownRefresh="pullDownRefreshObj"
                         :pullUpLoad="pullUpLoadObj"
                         :startY="parseInt(startY)"
                         @pullingDown="onPullingDown"
                         @pullingUp="onPullingUp">
        <ul class="list-content">
          <li class="list-item"
              v-for="item in items">{{item}}</li>
        </ul>
      </vue-better-scroll>
    </main>
    <button class="go-top"
            @click="scrollTo">返回顶部</button>
  </div>
</template>

<script>
  import VueBetterScroll from '../dist/vue-better-scroll'
  // import VueBetterScroll from './lib'

  let count = 1
  export default {
    name: 'app',
    components: { VueBetterScroll },
    data() {
      return {
        // 这个配置可以开启滚动条，默认为 false。当设置为 true 或者是一个 Object 的时候，都会开启滚动条，默认是会 fade 的
        scrollbarObj: {
          fade: true
        },
        // 这个配置用于做下拉刷新功能，默认为 false。当设置为 true 或者是一个 Object 的时候，可以开启下拉刷新，可以配置顶部下拉的距离（threshold） 来决定刷新时机以及回弹停留的距离（stop）
        pullDownRefreshObj: {
          threshold: 90,
          stop: 40
        },
        // 这个配置用于做上拉加载功能，默认为 false。当设置为 true 或者是一个 Object 的时候，可以开启上拉加载，可以配置离底部距离阈值（threshold）来决定开始加载的时机
        pullUpLoadObj: {
          threshold: 0,
          txt: {
            more: '加载更多',
            noMore: '没有更多数据了'
          }
        },
        startY: 0, // 纵轴方向初始化位置
        scrollToX: 0,
        scrollToY: 0,
        scrollToTime: 700,
        items: []
      }
    },
    mounted() {
      this.onPullingDown()
    },
    methods: {
      // 滚动到页面顶部
      scrollTo() {
        this.$refs.scroll.scrollTo(this.scrollToX, this.scrollToY, this.scrollToTime)
      },
      // 模拟数据请求
      getData() {
        return new Promise(resolve => {
          setTimeout(() => {
            const arr = []
            for (let i = 0; i < 10; i++) {
              arr.push(count++)
            }
            resolve(arr)
          }, 1000)
        })
      },
      onPullingDown() {
        // 模拟下拉刷新
        console.log('下拉刷新')
        count = 0
        this.getData().then(res => {
          this.items = res
          this.$refs.scroll.forceUpdate(true)
        })
      },
      onPullingUp() {
        // 模拟上拉 加载更多数据
        console.log('上拉加载')
        this.getData().then(res => {
          this.items = this.items.concat(res)
          if (count < 30) {
            this.$refs.scroll.forceUpdate(true)
          } else {
            this.$refs.scroll.forceUpdate(false)
          }
        })
      }
    }
  }
</script>

<style>
  .position-box {
    position: fixed;
    top: 40px;
    left: 0;
    right: 0;
    bottom: 0;
  }
</style>
```
#### forceUpdate
数据跟新后强制更新页面

#### pullingDown
触发时机：在一次下拉刷新的动作后，这个时机一般用来去后端请求数据。(触发事件在参数中需要开启 pullDownRefresh 相关配置 )

#### pullingUp
触发时机：在一次上拉加载的动作后，这个时机一般用来去后端请求数据。(触发事件在参数中需要开启 pullingUp 相关配置 )

## 2. 为什么使用vuex
1. 思考，当多个组件需要共享同一个状态时，如何实现？

当我们的应用遇到多个组件共享状态时，单向数据流的简洁性很容易被破坏：
* 多个视图依赖于同一状态。
* 来自不同视图的行为需要变更同一状态。

对于问题一，传参的方法对于多层嵌套的组件将会非常繁琐，并且对于兄弟组件间的状态传递无能为力。对于问题二，我们经常会采用父子组件直接引用或者通过事件来变更和同步状态的多份拷贝。以上的这些模式非常脆弱，通常会导致无法维护的代码。

于是就可以把状态抽离出来，单独放在一个地方管理，所有的组件都访问相同的状态对象。实现状态共享。


2. vuex是vue中专门用来做状态管理的库，借鉴了Flux和Redux的思想。

#### 官网：https://vuex.vuejs.org/zh/

    每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的状态 (state)。

但是Vuex 和单纯的全局对象有以下两点不同：

* Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
* 你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

## 3. Vuex的核心概念

* state state对象，保存了所有的应用状态
* Getter 可以认为是 store 的计算属性
* mutation 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation
* Action  Action 提交的是 mutation，而不是直接变更状态，可以包含异步操作
![image](http://note.youdao.com/yws/res/6785/C60C839A134542C1B42522506D399922)

## 4. 简单示例

1. 使用 vue-cli 3.x 创建项目
```
vue create demo
```
2. 安装vuex
```
npm install vuex --save
```
3. 引入
在main.js 引入 vuex
```
import Vuex from 'vuex';
Vue.use(Vuex)
```
4. 创建store.js 文件

```
import Vue from 'vue'
import Vuex from 'vuex';
Vue.use(Vuex)

// 状态store对象
const store = new Vuex.Store({

    // state是一个对象，用来保存所有的应用状态
    state: {
      count: 0
    },

    // 在mutations 里修改状态
    mutations: {
      increment (state) {
        state.count++
      }
    }
})
export default store;
```
5. 把store挂载到vue根示例对象上
```
import Vue from 'vue'
import App from './App.vue'
//引入定义好的store
import store from './store';

new Vue({
  store, //把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  render: h => h(App),
}).$mount('#app')

```

6. 使用

在 HelloWorld组件中使用：
```
<template>
  <div class="hello">
        <!--  从store中获取状态 -->
        {{this.$store.state.count}}

        <!-- 点击事件更新状态 -->
        <button @click="click"> click</button> 
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  methods: {
    click(){
      // 提交mutation，更新状态
      this.$store.commit('increment')
    }
  },
}
</script>
```

## 5. 学员练习
尝试添加按钮，实现减法

## 6. 提交载荷
你可以向 store.commit 传入额外的参数，即 mutation 的 载荷（payload）：
```
mutations: {
  increment (state, n) {
    state.count += n
  }
}
```
提交：
```
store.commit('increment', 10)
```

## 7. 使用vuex实现todolist

## Action




  

  




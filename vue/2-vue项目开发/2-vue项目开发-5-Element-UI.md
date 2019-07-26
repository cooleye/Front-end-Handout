## 1. State
```
computed: {
    ...mapState(["count","users","name"])
},
```
## 2. getter

```
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```
在组件中使用：
```
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```

你也可以通过让 getter 返回一个函数，来实现给 getter 传参。在你对 store 里的数组进行查询时非常有用
```
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
```

使用：
```
store.getters.getTodoById(2)
```

#### mapGetters
mapGetters 辅助函数可以将 store 中的 getter 映射到局部计算属性
```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```
如果你想将一个 getter 属性另取一个名字，使用对象形式：
```
mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

## 3. Mutations

使用mapMutations辅助函数：
```
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```


## 4. mapActions

```
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```


## 5. vuex 实现购物车




## 6. 动画

Vue 在插入、更新或者移除 DOM 时，提供多种不同方式的应用过渡效果。
包括以下工具：

* 在 CSS 过渡和动画中自动应用 class
* 可以配合使用第三方 CSS 动画库，如 Animate.css
* 在过渡钩子函数中使用 JavaScript 直接操作 DOM
* 可以配合使用第三方 JavaScript 动画库，如 Velocity.js


Vue 提供了 transition 的封装组件，把需要做动画的组件包裹起来
#### 示例1：
```
<template>
    <div id="demo">
  <button v-on:click="show = !show">
    Toggle
  </button>
  <transition name="fade">
    <p v-if="show">hello</p>
  </transition>
</div>
</template>

<script>
export default {
    data(){
        return {
            show: true
        }
    }
}
</script>

<style scoped>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
```

#### 过渡的类名
在进入/离开的过渡中，会有 6 个 class 切换。

1. v-enter：定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。
2. v-enter-active：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。
3. v-enter-to: 2.1.8版及以上 定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter 被移除)，在过渡/动画完成之后移除。
4. v-leave: 定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
5. v-leave-active：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。
6. v-leave-to: 2.1.8版及以上 定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave 被删除)，在过渡/动画完成之后移除。

![image](http://note.youdao.com/yws/res/6890/2B9F7FE30DE94BAF93BF8D81A46BCD4E)

注意：如果在<transition>上没有添加name属性，则类名前缀默认为v-，添加name属性，则使用 属性名作为前缀。

#### 示例二：
```
<template>
    <div>
        <button @click="show = !show">show toggle</button>
        <transition name="fade">
            <div v-if="show" class="box">
                    hello
            </div>
        </transition>
    </div>
</template>

<script>
export default {
    data(){
        return {
            show:false
        }
    }

}
</script>

<style  scoped>

.box{
    width: 100px;
    height: 100px;
    border: solid 1px #666;
}

/* 定义进入开始的状态 */
.fade-enter{
    background-color: #f00;
}
/* 定义进入结束的状态 */
.fade-enter-to{
    background-color: #0f0;
}
/* 动画进入过程的时间，延迟和曲线函数 */
.fade-enter-active{
    transition: background-color 5s;
}

/* 定义离开开始的状态 */
.fade-leave{
    background-color: #0f0;
}
/* 定义离开结束的状态 */
.fade-leave-to{
    background-color: #00f;
}
/* 定义离开过渡的过程时间，延迟和曲线函数。 */
.fade-leave-active{
    transition: background-color 5s;
}

</style>
```

#### css  animation 动画
```
<template>
    <div>  
    <div id="example-2">
        <button @click="show = !show">Toggle show</button>
        <transition name="bounce"  >
            <p v-if="show">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris facilisis enim libero, at lacinia diam fermentum id. Pellentesque habitant morbi tristique senectus et netus.</p>
        </transition>
    </div>
    </div>
</template>

<script>
export default {
    data(){
        return {
            show: true
        }
    }
}
</script>   

<style scoped>
    
.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-in .5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}

</style>
```

#### 自定义过渡类名,使用Animate.css
我们可以通过以下特性来自定义过渡类名：

- enter-class
- enter-active-class
- enter-to-class (2.1.8+)
- leave-class
- leave-active-class
- leave-to-class (2.1.8+)

他们的优先级高于普通的类名，这对于 Vue 的过渡系统和其他第三方 CSS 动画库，如 Animate.css 结合使用十分有用。
```
<template>
    <div>
        
        <button @click="show = !show">
            Toggle render
        </button>
        <transition
            name="custom-classes-transition"            
            enter-active-class="animated tada"
            leave-active-class="animated bounceOutRight"
        >
            <p v-if="show">hello</p>
        </transition>

    </div>
</template>

<script>
export default {
    data(){
        return {
            show: true
        }
    }
}
</script>

<style scoped>
@import url("https://cdn.jsdelivr.net/npm/animate.css@3.5.1");
</style>
```

#### JavaScript 钩子
可以在属性中声明 JavaScript 钩子
```
<template>
    <div>
        
        <button @click="show = !show">show toggle</button>
        <transition

            name="bounce"
            v-on:before-enter="beforeEnter"
            v-on:enter="enter"
            v-on:after-enter="afterEnter"
            v-on:enter-cancelled="enterCancelled"

            v-on:before-leave="beforeLeave"
            v-on:leave="leave"
            v-on:after-leave="afterLeave"
            v-on:leave-cancelled="leaveCancelled"
            >

            <div v-if="show" class="box">
                    hello
            </div>
        </transition>
    </div>
</template>

<script>
export default {

    data(){
        return {
            show:false
        }
    },
    
    methods: {
  // --------
  // 进入中
  // --------

  beforeEnter: function (el) {
    //    el.style.left = "0";
    //    el.style.top = "0";
    // ...
    console.log("beforeEnter...")
  },
  // 当与 CSS 结合使用时
  // 回调函数 done 是可选的
  enter: function (el, done) {
      console.log("enter...")

    // ...
    done()
  },
  afterEnter: function (el) {
      console.log("afterEnter...")
    //    el.style.left = "400px";
    //    el.style.top = "400px";
    // ...
  },
  enterCancelled: function (el) {
      console.log("enterCancelled...")
    // ...
  },

  // --------
  // 离开时
  // --------

  beforeLeave: function (el) {
      console.log("beforeLeave...")
     
    // ...
  },
  // 当与 CSS 结合使用时
  // 回调函数 done 是可选的
  leave: function (el, done) {
      console.log("leave...")
    // ...
    done()
  },
  afterLeave: function (el) {
      console.log("afterLeave...")
    // ...
  },
  // leaveCancelled 只用于 v-show 中
  leaveCancelled: function (el) {
      console.log("leaveCancelled...")
    // ...
  }
}
}
</script>
<style scoped>
</style>
```

#### 使用velocity.js 动画库
```
<template>
    <div id="example-4">
    <button @click="show = !show">
        Toggle
    </button>
    <transition
        v-on:before-enter="beforeEnter"
        v-on:enter="enter"
        v-on:leave="leave"
        v-bind:css="false"
    >
        <p v-if="show">
        Demo
        </p>
    </transition>
    </div>
</template>

<script>

import  '../assets/velocity.js';
export default {
    data(){
        return {
            show: true
        }
    },
    methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
      el.style.transformOrigin = 'left'
    },
    enter: function (el, done) {
      Velocity(el, { opacity: 1, fontSize: '1.4em' }, { duration: 300 })
      Velocity(el, { fontSize: '1em' }, { complete: done })
    },
    leave: function (el, done) {
      Velocity(el, { translateX: '15px', rotateZ: '50deg' }, { duration: 600 })
      Velocity(el, { rotateZ: '100deg' }, { loop: 2 })
      Velocity(el, {
        rotateZ: '45deg',
        translateY: '30px',
        translateX: '30px',
        opacity: 0
      }, { complete: done })
    }
  }
}
</script>
```



## 7. Element-UI

#### 官网：https://element.eleme.cn/#/zh-CN/component/installation

#### 安装

#### 引入


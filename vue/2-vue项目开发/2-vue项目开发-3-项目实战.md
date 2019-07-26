## vue项目实战

## 1. 过滤器 💎💎💎
1. 定义过滤器

```
filters:{
  uppercase(value){
      return value.toUpperCase()
  }
}
```
2. 使用过滤器

```
{{name | uppercase}}
```
3. 使用多个过滤器

```
{{name | uppercase | slice}}
```
4. 过滤器参数

```
priceFamt(value,sym,num){
  return sym + value.toFixed(num)
}
```
使用
```
<h1>{{price | priceFamt("$",2) }} </h1>
```
5. 定义全局过滤器 
```
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

```
  

## 2. 自定制指令💎💎

```
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```

注册局部指令：
```
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```

* bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
* inserted：被绑定元素插入父节点时调用
* update：所在组件的 VNode 更新时调用
* componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。
* unbind：只调用一次，指令与元素解绑时调用。

指令定义示例：
```
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```
使用：
```
<div v-color-swatch="color"> box </div>
```

自定义 v-myshow指令
```
Vue.directive('myshow',function(el,binding){
  el.style.display = binding.value ? "block" : "none"
})
```


## 3. vue-axios
Github:https://github.com/imcvampire/vue-axios
安装：
```
npm install --save axios vue-axios
```
在main.js引入：
```
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios)
```
使用：
```
Vue.axios.get(api).then((response) => {
  console.log(response.data)
})

this.axios.get(api).then((response) => {
  console.log(response.data)
})

this.$http.get(api).then((response) => {
  console.log(response.data)
})
```

## 4. ref
一个元素添加ref属性，例如：
```
<box ref="mybox"></box>
```
可以使用 `this.$refs.mybox`获取到这个组件

## 5. vue-swiper
#### github：https://github.com/surmon-china/vue-awesome-swiper

1. 安装  `npm install vue-awesome-swiper --save`
2. 全局注册：
```
import Vue from 'vue'
import VueAwesomeSwiper from 'vue-awesome-swiper'

// require styles
import 'swiper/dist/css/swiper.css'

Vue.use(VueAwesomeSwiper, /* { default global options } */)
```
3. 或者在组件内局部注册
```
// require styles
import 'swiper/dist/css/swiper.css'

import { swiper, swiperSlide } from 'vue-awesome-swiper'

export default {
  components: {
    swiper,
    swiperSlide
  }
}
```
4. 使用：
```
<!-- The ref attr used to find the swiper instance -->
<template>
  <swiper :options="swiperOption" ref="mySwiper" @slideChangeTransitionEnd="slideChangeEnd">
    <!-- slides -->
    <swiper-slide>I'm Slide 1</swiper-slide>
    <swiper-slide>I'm Slide 2</swiper-slide>
    <swiper-slide>I'm Slide 3</swiper-slide>
    <swiper-slide>I'm Slide 4</swiper-slide>
    <swiper-slide>I'm Slide 5</swiper-slide>
    <swiper-slide>I'm Slide 6</swiper-slide>
    <swiper-slide>I'm Slide 7</swiper-slide>
    <!-- Optional controls -->
    <div class="swiper-pagination"  slot="pagination"></div>
    <div class="swiper-button-prev" slot="button-prev"></div>
    <div class="swiper-button-next" slot="button-next"></div>
    <div class="swiper-scrollbar"   slot="scrollbar"></div>
  </swiper>
</template>

<script>
  export default {
    name: 'carrousel',
    data() {
      return {
        swiperOption: {
          // some swiper options/callbacks
          // 所有的参数同 swiper 官方 api 参数
          // ...
        }
      }
    },
    computed: {
      swiper() {
        return this.$refs.mySwiper.swiper
      }
    },
    mounted() {
      // current swiper instance
      // 然后你就可以使用当前上下文内的swiper对象去做你想做的事了
      console.log('this is current swiper instance object', this.swiper)
      this.swiper.slideTo(3, 1000, false)
    },
    methods: {
        slideChangeEnd(){
            console.log('slideChangeEnd')
        }
    },
  }
</script>
```



## 6. 项目练习
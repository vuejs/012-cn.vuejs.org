title: 扩展 Vue
type: guide
order: 14
---

## 使用 Mixin 进行扩展

Mixin 是一种为 Vue 组件分发可复用功能的灵活的方式。你可以像写一个普通 Vue 组件的选项对象一样写出一个 mixin：

``` js
// mixin.js
module.exports = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('hello from mixin!')
    }
  }
}
```

``` js
// test.js
var myMixin = require('./mixin')
var Component = Vue.extend({
  mixins: [myMixin]
})
var component = new Component() // -> "hello from mixin!"
```

## 使用插件进行扩展

通常插件会为 Vue 添加一个全局的功能。

### 撰写插件

你可以撰写以下几种典型类型的插件：

1. 添加一个或几个全局方法。比如 [vue-element](https://github.com/vuejs/vue-element)
2. 添加一个或几个全局资源：指令、过滤器、过渡效果等。比如 [vue-touch](https://github.com/vuejs/vue-touch)
3. 通过绑定到 `Vue.prototype` 的方式添加一些 Vue 实例方法。这里有个约定，就是 Vue 的实例方法应该带有 `$` 前缀，这样就不会和用户的数据和方法产生冲突了。

``` js
exports.install = function (Vue, options) {
  Vue.myGlobalMethod = ...          // 1
  Vue.directive('my-directive', {}) // 2
  Vue.prototype.$myMethod = ...     // 3
}
```

### 使用插件

假设我们使用的构建系统是 CommonJS，则：

``` js
var vueTouch = require('vue-touch')
// use the plugin globally
Vue.use(vueTouch)
```

你也可以向插件里传递额外的选项：

```js
Vue.use(require('my-plugin'), {
  /* pass in additional options */
})
```

<<<<<<< HEAD
## 现有的工具

- [vue-resource](https://github.com/vuejs/vue-resource): 一个插件，为用XMLHttpRequest或JSONP生成网络请求、响应提供服务。
- [vue-devtools](https://github.com/vuejs/vue-devtools)：一个用来 debug Vue.js 应用程序的 Chrome 开发者工具扩展。
- [vue-touch](https://github.com/vuejs/vue-touch)：添加基于 Hammer.js 的触摸手势的指令。
- [vue-element](https://github.com/vuejs/vue-element): 用 Vue.js 注册 Custom Elements。
- [用户贡献的工具列表](https://github.com/yyx990803/vue/wiki/User-Contributed-Components-&-Tools)
=======
## Existing Plugins & Tools

- [vue-resource](https://github.com/vuejs/vue-resource): A plugin that provides services for making web requests and handle responses using a XMLHttpRequest or JSONP.
- [vue-validator](https://github.com/vuejs/vue-validator): A plugin for form validations.
- [vue-devtools](https://github.com/vuejs/vue-devtools): A Chrome devtools extension for debugging Vue.js applications.
- [vue-touch](https://github.com/vuejs/vue-touch): Add touch-gesture directives using Hammer.js.
- [vue-element](https://github.com/vuejs/vue-element): Register Custom Elements with Vue.js.
- [List of User Contributed Tools](https://github.com/yyx990803/vue/wiki/User-Contributed-Components-&-Tools)
>>>>>>> 0.12.8

下一页: [常用FAQs](/guide/faq.html).

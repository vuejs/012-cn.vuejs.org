title: 全局 API
type: api
order: 5
---

### Vue.config

`Vue.config` 是包含 Vue 全局设置的对象。下面列出了所有可设置变量及其默认值：

``` js
{
  // 启动调试模式。详见下文
  debug: true,
  // 启用严格模式，详见下文
  strict: false,
  // 指令的特性前缀
  prefix: 'v-',
  // 插入分隔符
  // 对于插入HTML，添加一个额外的最外层字符
  delimiters: ['{&#123;', '&#125;}'],
  // 抑制 warnings？
  silent: false,
  // 插入 mustache 绑定?
  interpolate: true,
  // (对 directives 和 watchers)使用异步更新？
  async: true,
  // 允许改变所观察的Array的原型链？
  proto: true
}
```

你可以直接对其修改，例如：

``` js
Vue.config.debug = true // 开启调试模式
```

**调试模式**

当 `Vue.config.debug` 设置为 true 时，Vue 会

1. 打印所有警告的栈的追踪记录
2. 让所有 DOM 里的锚结点都采用注释结点。这会让审查元素结构变得更容易。

<p class="tip">调试模式在压缩的生产环境(minified production builds)下是不可用的</p>

**严格模式**

默认情况下，Vue 组件继承了整个类继承链的所有资源 (通过 `Vue.extend`) 和父视图。在严格模式下，组件只能继承从类继承的资源，不能从父视图层次继承。当启用了严格的模式时，资源应该是全局性的，或者是依赖于需要它们的组件。使用严格模式，能更好的封装组件和增加大型的项目的可重用性。

**改变分隔符 (Delimiters)**

当分隔符 (delimiters) 是设置为插入文本时，那么设置为插入HTML的分隔符可以通过在两边的最外层加上符号来生成。

``` js
Vue.config.delimiters = ['(%', '%)']
// (% %)是文本的标签
// 则((% %))是HTML的标签
```

### Vue.extend( options )

- **options** `Object`

创建一个基类 Vue 构造函数的"子类"。所有的[实例化选项](../api/options.html)都可以在此用到。其中需要特殊提及的是 `el` 和 `data`，在这个类里他们必须作为函数使用。

在所有组件(components)被初始化之前，`Vue.extend()`被组件选项隐式地调用。关于组件更多的细节，可详见[组件系统](../guide/components.html)。

**例子**

``` html
<div id="mount-point"></div>
```

``` js
// create reusable constructor
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>'
})
// create an instance of Profile
var profile = new Profile({
  data: {
    firstName : 'Walter',
    lastName  : 'White',
    alias     : 'Heisenberg'
  }  
})
// mount it on an element
profile.$mount('#mount-point')
```

将输出：

``` html
<p>Walter White aka Heisenberg</p>
```

### Vue.directive( id, [definition] )

- **id** `String`
- **definition** `Function` or `Object` *可选的*

注册或取得一个全局自定义的指令。详情见[编写自定义指令](../guide/custom-directive.html)。

### Vue.elementDirective( id, [definition] )

- **id** `String`
- **definition** `Function` or `Object` *可选的*

注册或取得一个全局自定义的元素指令。详情见[元素指令](../guide/custom-directive.html)。

### Vue.filter( id, [definition] )

- **id** `String`
- **definition** `Function` *可选的*

注册或取得一个全局自定义的过滤器。详情见[编写自定义过滤器](../guide/custom-filter.html)。

### Vue.component( id, [definition] )

- **id** `String`
- **definition** `Function Constructor` or `Object` *可选的*

注册或取得一个全局自定义的组件。详情见[组件系统](../guide/components.html)。

### Vue.transition( id, [definition] )

- **id** `String`
- **definition** `Object` *可选的*

注册或取得一个全局 JavaScript 转换效应(transition effect)定义。详见[JavaScript转换](../guide/transitions.html#JavaScript_Functions)的指导。

### Vue.partial( id, [definition] )

- **id** `String`
- **definition** `String | Node` *可选的*

注册或取得一个全局的partial。定义可以是一个string模板，一个以`#`开头的querySelector，一个DOM元素(其内嵌HTML作为string模板使用)或者一个DocumentFragment

**例子**
HTML

``` html
<div id="demo">
  &#123;&#123;> avatar&#125;&#125;
</div>
```

JavaScript

``` js
Vue.partial('avatar', '&lt;img v-attr="src:avatarURL"&gt;')

new Vue({
  el: '#demo',
  data: {
    avatarURL: '/images/avatar.jpg'
  }    
})
```

将输出:

``` html
<div id="demo">
  <img src="/images/avatar.jpg">
</div>
```

### Vue.nextTick( callback )

- **callback** `Function`

Vue.js 批量处理视图更新并对其异步执行。如果可用的话它会使用 `requestAnimationFrame` 并返回到 `setTimeout(fn, 0)`。这个方法在下一个视图更新后调用回调，当你想一直等待到该视图被更新了，用这个方法会很好。

### Vue.partial( id, [partial] )

- **id** `String`
- **partial** `String` *optional*

Register or retrieve a global template partial string. For more details see [Partial](/api/elements.html#partial).

### Vue.use( plugin, [args...] )

- **plugin** `Object` or `Function`
- **args...** *可选的*

挂载一个 Vue.js 的插件。如果该插件是一个对象，它必须有一个 `install` 的方法。如果它本身就是一个方法，它必须被作为安装方法来对待。安装方法将会被 Vue 作为一个参数来调用。更多详情请阅[插件](../guide/extending.html#使用插件进行扩展)。

### Vue.util

公开内部的 `util` 模块。该模块包含一些列实用方法(utility methods)。这是被为用来编写高级插件/指令，因此你必须查看源码来确定哪些是可操作的。

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
  // (对 directives 和 watchers) 使用异步更新？
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

<p class="tip">调试模式在压缩过的生产版本中是不可用的。</p>

**严格模式**

默认情况下，Vue 组件从继承链（通过 `Vue.extend()` 创建）和它在视图里的父组件那里继承所有的资源。在严格模式下，组件只能继承从类继承的资源，不能从父视图层次继承。当启用了严格的模式时，资源应该是全局性的，或者是依赖于需要它们的组件。使用严格模式，能更好的封装组件和增加大型的项目的可重用性。

**改变分隔符 (Delimiters)**

如果设置了文本插值的分隔符，HTML 插值的分隔符也将改变。只需要用文本分隔符符最外面的字符再包裹一层：

``` js
Vue.config.delimiters = ['(%', '%)']
// (% %) 是文本插值的标签
// 则 ((% %)) 是 HTML 插值的标签
```

### Vue.extend( options )

- **options** `Object`

创建一个 Vue 的子类。所有的[实例化选项](../api/options.html)都可以在此通用。其中需要特殊提及的是 `el` 和 `data`，在这里它们必须以函数形式出现。

直接用对象形式提供的组件在实例化之前 Vue 会在内部对其隐式调用 `Vue.extend()`。关于组件更多的细节，参见[组件系统](../guide/components.html)。

**例子**

``` html
<div id="mount-point"></div>
```

``` js
// 创建可复用的 Profile 组件构造函数
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>'
})
// 创建一个 Profile 组件的实例
var profile = new Profile({
  data: {
    firstName : 'Walter',
    lastName  : 'White',
    alias     : 'Heisenberg'
  }  
})
// 挂载到元素上
profile.$mount('#mount-point')
```

结果：

``` html
<p>Walter White aka Heisenberg</p>
```

### Vue.nextTick( callback )

- **callback** `Function`

延迟执行回调到下一次 DOM 更新循环。在修改数据后调用它，等待 DOM 更新。详见 [理解异步更新](/guide/directives.html#理解异步更新)。

### Vue.directive( id, [definition] )

- **id** `String`
- **definition** `Function` or `Object` *optional*

注册或取得一个全局自定义指令。详情见[编写自定义指令](../guide/custom-directive.html)。

### Vue.elementDirective( id, [definition] )

- **id** `String`
- **definition** `Function` or `Object` *optional*

注册或取得一个全局自定义元素指令。详情见[元素指令](../guide/custom-directive.html#元素指令)。

### Vue.filter( id, [definition] )

- **id** `String`
- **definition** `Function` *optional*

注册或取得一个全局自定义过滤器。详情见[编写自定义过滤器](../guide/custom-filter.html)。

### Vue.component( id, [definition] )

- **id** `String`
- **definition** `Function Constructor` or `Object` *optional*

注册或取得一个全局自定义组件。详情见[组件系统](../guide/components.html)。

### Vue.transition( id, [definition] )

- **id** `String`
- **definition** `Object` *optional*

注册或取得一个全局 JavaScript 过渡效果定义。详见教程中[JavaScript 过渡效果](../guide/transitions.html#JavaScript_Functions)的相关章节。

### Vue.partial( id, [definition] )

- **id** `String`
- **definition** `String | Node` *optional*

注册或获取全局的分模板（partial）。详见 [Partial](/api/elements.html#partial)。

### Vue.use( plugin, [args...] )

- **plugin** `Object` or `Function`
- **args...** *optional*

加载一个 Vue.js 的插件。如果该插件是一个对象，它必须有一个可调用的 `install` 方法。如果它本身就是一个函数，它会直接被作为安装函数来调用。Vue 会将其本身作为一个参数传递到安装函数里。详见[插件](../guide/extending.html#使用插件进行扩展)。

### Vue.util

公开内部的 `util` 模块。该模块包含一些常用的工具函数。此模块只是为了方便编写高级插件/指令，因此你必须查看源码来了解其中细节。

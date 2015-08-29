title: 组件选项
type: api
order: 2
---

## 数据

### data

- **类型：** `Object | Function`
- **限制：** 在 `Vue.extend()` 中使用时只接受 `Function`.

Vue实例的数据对象. 可以通过 `vm.$data` 访问:

```js
var data = { a: 1 }
var vm = new Vue({
  data: data
})
vm.$data === data // -> true
```

Vue 实例会代理其数据对象上的所有属性，因此你可以直接在 Vue 实例上进行数据操作，这些变化会同步到数据对象里：

```js
vm.a   // -> 1
vm.a = 2
data.a // -> 2
data.a = 3
vm.a   // -> 3
```

注意以 `_` 或 `$` 开头的属性将不会被代理在 Vue 实例上，因为它们会和 Vue 的内部属性和 API 方法产生冲突。你只能通过 `vm.$data._property` 的方式访问它们。

数据对象必须是合法的 JSON 对象 (不能有循环引用)。使用时和普通对象并没有区别，`JSON.stringify` 序列化的结果也完全一样。同时，同一个数据对象可以被多个 Vue 实例共享。

这里有一个特殊的情况，就是在 `Vue.extend()` 中使用 `data` 选项时，由于我们不想让同一个数据对象被所有同一个构造函数所创建实例的共享，所以必须提供一个工厂函数来返回一个全新的对象:

``` js
var MyComponent = Vue.extend({
  data: function () {
    return {
      message: 'some default data.',
      object: {
        fresh: true
      }
    }
  }
})
```

<p class="tip">在内部, Vue.js 会在被观测的数据对象上创建一个隐藏属性 `__ob__`， 然后通过递归遍历，将所有可枚举的属性转化为 getters 和 setters，从而实现依赖收集。</p>

### props

- **Type:** `Array | Object`

props 代表了当前组件预期从父组件处获得的外部数据。props 选项有一个简单的基于数组的写法，也有一个可选的基于对象的写法。对象写法让你对每个 prop 进行单独的高级配置，比如类型检查、自定义验证和默认值等等。

**示例:**

``` js
Vue.component('param-demo', {
  props: ['size', 'myMessage'], // 数组写法
  compiled: function () {
    console.log(this.size)    // -> 100
    console.log(this.myMessage) // -> 'hello!'
  }
})
```

这里需要注意，因为 HTML 特性是大小写不敏感的，所以当一个 prop 作为特性出现在模板里的时候，你需要使用连字符格式：

``` html
<!-- myMessage 需要写成 my-message -->
<param-demo size="100" my-message="hello!"></param-demo>
```

基于对象的写法如下所示：

``` js
Vue.component('prop-validation-demo', {
  props: {
    size: Number,
    name: {
      type: String,
      required: true
    }
  }
})
```

下面的组件用法会导致两个警告： "size" 的类型不匹配，以及缺少必需的 prop "name" 。

``` html
<prop-validation-demoo size="hello">
</prop-validation-demo>
```

更多关于数据传递的细节，请阅读教程中的以下章节：

- [传递回调 prop](/guide/components.html#传递回调作为_prop)
- [prop 绑定类型](/guide/components.html#prop_绑定类型)
- [prop 验证规则](/guide/components.html#prop_验证规则)

#### 关于连字符格式

HTML 特性名大小写不敏感，所以我们在模板中通常使用连字符格式代替驼峰格式。在使用连字符格式的 `props` 特性的时候，需要注意一些特殊情况：

1. 如果该特性是一个数据特性， `data-` 前缀会被自动去掉。比如 `data-size` 会被解析为 `vm.size`。

2. 如果该特性仍然包含连字符 (`-`)，它会被驼峰化。比如 `my-prop` 会被解析为 `vm.myProp`。这是因为在模板里访问包含连字符的顶层属性不太方便：表达式 `my-param` 将会被解析为一个减法表达式，除非你使用尴尬的 `this['my-param']` 写法。

### methods

- **类型：** `Object`

Methods 选项中包含的方法函数将会被混入到 Vue 实例中。你可以直接在 Vue 实例上访问这些方法，也可以在指令表达式里使用他们。 所有方法的 `this` 上下文都指向其所属的 Vue 实例。

**示例：**

```js
var vm = new Vue({
  data: { a: 1 },
  methods: {
    plus: function () {
      this.a++
    }
  }
})
vm.plus()
vm.a // 2
```

### computed

- **类型：** `Object`

Computed 选项中所包含的内容将会被作为**计算属性**混入到 Vue 实例中。所有计算属性的 getter/setter 函数中 `this` 上下文都指向其所属的 Vue 实例。

**示例：**

```js
var vm = new Vue({
  data: { a: 1 },
  computed: {
    // 只需要 getter 时，直接用一个函数即可
    aDouble: function () {
      return this.a * 2
    },
    // 同时提供 getter 与 setter
    aPlus: {
      get: function () {
        return this.a + 1
      },
      set: function (v) {
        this.a = v - 1
      }
    }
  }
})
vm.aPlus   // -> 2
vm.aPlus = 3
vm.a       // -> 2
vm.aDouble // -> 4
```

## DOM

### el

- **类型：** `String | HTMLElement | Function`
- **限制：** 在 `Vue.extend()` 中使用时只接受 `Function` 类型。

为 Vue 实例提供一个作为挂载点的 DOM 元素。选项的值可以是一个 CSS 选择器，一个 HTML 元素，或是一个创建并返回 HTML 元素的函数。这个元素主要是起一个挂载点的作用：如果同时提供了模板选项，则该元素将被模板的内容替换 (除非 replace 选项为 false）。

在 `Vue.extend()` 中，此选项必须提供一个函数，以避免所有的实例共享同一个元素。

如果在初始化时提供了这个选项，实例将立即进入编译阶段。不然则需要手动调用 `vm.$mount()` 才能触发编译。

### template

- **类型：** `String`

此选项所提供的字符串模板会被用来生成 Vue 实例所管理的 HTML 内容。默认情况下，该模板会**替代**挂载的目标元素。当 `replace` 选项被设置为 `false` 时，模板则会被插入到挂载的元素内部。这两种情况下，挂载元素内部任何存在的原始内容都会被忽略，除非模板里包含了 [内容插入点](/guide/components.html#内容插入)。

如果该字符串以 `#` 开头，它将会被当做 CSS 的 ID 选择器处理，并使用被选取元素的 `innerHTML` 作为字符串模板。利用这个功能，可以使用常见的 `<script type="x-template">` 方式来引入模板。

注意，如果一个模板包含多个顶层节点，那么该实例将会成为一个 [片段实例](/guide/best-practices.html#片段实例) - 也即没有单独根节点的实例。

<p class="tip">Vue.js 使用基于 DOM 的模板编译机制。编译器遍历所有 DOM 元素，查找指令并创建数据绑定。这意味着所有的 Vue.js 模板都是可以解析的 HTML，HTML 被浏览器解析后生成 DOM。Vue.js 将字符串模板转为 DOM 片段并缓存，因此在创建新的 Vue 实例时，只需要直接复制这些片段即可。如果想让模板是完全符合标准的 HTML，你可以将指令前缀设置为 `data-`。</p>

### replace

- **类型：** `Boolean`  
- **默认值：** `true`
- **限制：** 需要和 **template** 选项协同使用

是否用模板替换正在被挂载的元素。如果设置为 `false` ，该模板将会覆盖元素内部的内容，而不是替代元素本身。

**示例**:

``` html
<div id="replace"></div>
```

``` js
new Vue({
  el: '#replace',
  template: '<p>replaced</p>'
})
```

编译结果：

``` html
<p>replaced</p>
```

相比之下, 当 `replace` 被设置成 `false` 时：

``` html
<div id="insert"></div>
```

``` js
new Vue({
  el: '#insert',
  replace: false,
  template: '<p>inserted</p>'
})
```

编译结果：

``` html
<div id="insert">
  <p>inserted</p>
</div>
```

## 生命周期

所有生命周期钩子函数的 `this` 上下文都指向其所属的 Vue 实例。除了调用选项中的钩子函数之外，Vue 实例也会触发对应的事件，事件的形式为 `"hook:<hookName>"`。以 `created` 钩子为例，将会触发 `hook:created` 事件。

### created

- **类型：** `Function`

在实例被创建的时候同步调用。在这个阶段，实例已经完成了包含以下内容的准备工作：数据观察，计算属性，方法，以及事件回调。但 DOM 编译还没开始，`vm.$el` 此时尚不可用。

### beforeCompile

- **类型：** `Function`

在编译开始之前调用。

### compiled

- **类型：** `Function`

在编译完成后调用。在这个阶段，所有的指令都已经完成绑定，数据变化会触发DOM更新。但此时尚不能保证 `$el` 已经被插入到DOM中。

### ready

- **类型：** `Function`

在编译完成后**并且** `$el` 第一次插入文档时调用，也就是刚好在第一次 `attached` 钩子之后调用。注意只有通过指令或 Vue 实例方法，比如 `$appendTo()` 插入才会触发 ready。

### attached

- **类型：** `Function`

当 `vm.$el` 被一个指令或是 vm 实例方法（例如`$appendTo()`）插入到DOM里的时候调用。注意直接操作 `vm.$el` **不会**触发这个事件。

### detached

- **类型：** `Function`

当 `vm.$el` 被一个指令或是 vm 实例方法从 DOM 里移除的时候调用。注意直接操作 `vm.$el` **不会**触发这个事件。

### beforeDestroy

- **类型：** `Function`

在一个 Vue 实例被销毁之前调用。这个时候，实例的绑定和指令仍工作正常。

### destroyed

- **类型：** `Function`

在一个 Vue 实例被销毁之后调用。当这个钩子被调用时，该 Vue 实例的所有指令都已经被解除绑定，所有子实例也已经被销毁。

注意如果有一个离开过渡效果，`destroyed` 会在过渡效果结束**之后**才被调用。

## 资源

以下选项用于注册一个组件的私有资源。这些资源只能被该 Vue 实例及其子实例访问。所有的资源选项都应该是一个对象，键名即是该资源的 id，值则是对应的资源本身。

### directives

- **类型：** `Object`

需要注册的指令。更多细节请参考 [自定义指令](../guide/custom-directive.html).

### elementDirectives

- **类型：** `Object`

需要注册的元素指令。更多细节请参考 [元素指令](/guide/custom-directive.html#元素指令)。

### filters

- **类型：** `Object`

需要注册的过滤器。更多细节请参考 [自定义过滤器](../guide/custom-filter.html).

### components

- **类型：** `Object`

需要注册的组件。更多细节请参考 [组件系统](../guide/components.html).

### transitions

- **类型：** `Object`

需要注册的过渡效果。更多细节请参考 [过渡效果](../guide/transitions.html)。

### partials

- **Type:** `Object`

需要注册的模板片段。更多细节请参考 [Partial](/api/elements.html#partial)。

## 其他

### inherit

- **类型：** `Boolean`
- **默认值:** `false`

是否继承父组件的数据作用域. 当设置为 `true` 的时候你可以：

1. 在当前组件模板里绑定父组件的数据属性；
2. 通过原型继承直接访问父组件的属性。

需要注意，当使用 `inherit: true` 的时候，**子实例也可以改变父实例的属性值**，因为所有 Vue 实例的数据属性都是 getter/setters。

**示例：**

``` js
var parent = new Vue({
  data: { a: 1 }
})
var child = parent.$addChild({
  inherit: true,
  data: { b: 2 }
})
child.a  // -> 1
child.b  // -> 2
// 下面这行将会修改 parent.a 的值，
// 而不是在子实例上创建一个新的属性!
child.a = 2
parent.a // -> 2
```

### events

注册事件回调。该选项对象的键值是要注册的事件名，值就是相应的回调函数值。注意这里监听的是 Vue 的事件而不是 DOM 事件。值也可以是一个存在于当前组件上的方法名字符串。Vue实例会在初始化的时候对每一个事件/回调调用 `vm.$on()`。

**示例：**

``` js
var vm = new Vue({
  events: {
    'hook:created': function () {
      console.log('created!')
    },
    greeting: function (msg) {
      console.log(msg)
    },
    // 也可以用方法名字符串
    bye: 'sayGoodbye'
  },
  methods: {
    sayGoodbye: function () {
      console.log('goodbye!')
    }
  }
}) // -> created!
vm.$emit('greeting', 'hi!') // -> hi!
vm.$emit('bye')             // -> goodbye!
```

### watch

- **类型**: `Object`

注册数据观察回调。该选项对象的键名是要监听的表达式，而值是对应的回调函数。值也可以是一个方法名字符串，或者是一个包含额外选项的对象。 Vue 实例将会在初始化的时候对该对象的每个条目调用 `$watch()` 。

**示例：**

``` js
var vm = new Vue({
  data: {
    a: 1
  },
  watch: {
    'a': function (val, oldVal) {
      console.log('new: %s, old: %s', val, oldVal)
    },
    // 方法名字符串
    'b': 'someMethod',
    // 深度观察
    'c': {
      handler: function (val, oldVal) { /* ... */ },
      deep: true
    }
  }
})
vm.a = 2 // -> new: 2, old: 1
```

### mixins

- **类型**: `Array`

`mixins` 接受一个 mixin 对象数组，并会将每个 mixin 所包含的内容混入到当前组件中。这些混入对象可以像一般的实例对象一样包含实例选项，选项将被合并，合并逻辑同 `Vue.extend()`。举例来说，如果混入对象有一个 `created` 钩子，组件本身也有一个，则两个钩子都会被调用。

混入的钩子函数会被按照它们的出现顺序调用，并且会在宿主组件本身的钩子函数之前被调用。

**示例：**

``` js
var mixin = {
  created: function () { console.log(1) }
}
var vm = new Vue({
  created: function () { console.log(2) },
  mixins: [mixin]
})
// -> 1
// -> 2
```

### name

- **类型**: `String`
- **限制:** 仅在使用 `Vue.extend()` 的时候有效。

当在控制台里观察一个扩展过的 Vue 组件的时候，默认的构造函数名是 `VueComponent` ，并不是很有用。但你可以在使用 `Vue.extend()` 的时候添加一个 `name` 选项来自定义在控制台里输出的构造函数名。这个字符串会被驼峰化，然后作为组件的构造函数的名字使用。

**示例：**

``` js
var Ctor = Vue.extend({
  name: 'cool-stuff'
})
var vm = new Ctor()
console.log(vm) // -> CoolStuff {$el: null, ...}
```

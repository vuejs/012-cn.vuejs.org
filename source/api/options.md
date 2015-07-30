title: 组件选项
type: api
order: 2
---

## Data

### data

- **类型：** `Object | Function`
- **局限：** `Vue.extend()`只接受`Function`.

Vue实例的数据对象. 可以通过`vm.$data`访问:

```js
var data = { a: 1 }
var vm = new Vue({
  data: data
})
vm.$data === data // -> true
```

Vue实例会通过代理方法访问它的所有属性，因此你可以给Vue实例添加属性，然后这些变化会同步到数据对象里：

```js
vm.a   // -> 1
vm.a = 2
data.a // -> 2
data.a = 3
vm.a   // -> 3
```

数据对象必须是JSON格式 (不能有循环引用). 就像普通对象一样使用，而且支持`JSON.stringify`并可以在不同Vue实例中分享.

这里有一个特殊的情况，就是传递`data`参数到`Vue.extend()`时. 因为我们不想让嵌套对象被所有通过`Vue.extend()`扩展而生成实例共享，所以必须提供一个函数来返回一个数据对象的副本:

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

<p class="tip">在内部, Vue.js会创建一个隐藏属性`__ob__`， 然后通过递归循环转换所有可枚举的属性到getters和setters开实现依赖收集. 以`$`和`_`开头的属性会被跳过.</p>

### props

- **Type:** `Array | Object`

一个从父组件暴露出来的特性的列表/哈希。它有一个简单的基于数组的语法。还有一个可选的基于对象的语法，允许进行高级配置，比如类型检查、自定义验证和默认值。

**Example:**

``` js
Vue.component('param-demo', {
  props: ['size', 'myMessage'], // simple syntax
  compiled: function () {
    console.log(this.size)    // -> 100
    console.log(this.myMessage) // -> 'hello!'
  }
})
```

注意到因为 HTML 特性是区分大小的，所以当 props 作为一个特性出现在模板里的时候，你需要使用 props 带有连字符的形式：

``` html
<param-demo size="100" my-message="hello!"></param-demo>
```

更多关于数据传递的细节，请确保阅读指南中的以下章节：

- [属性绑定类型](/guide/components.html#Prop_Binding_Types)
- [传递回调作为属性](/guide/components.html#Passing_Callbacks_as_Props)

可选的基于对象的语法如下所示：

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

下面的组件用法会导致两个警告： size 的类型不匹配，和缺失要求的属性 "name" 。

``` html
<prop-validation-demoo size="hello">
</prop-validation-demo>
```

更多关于基于对象的语法和属性验证的内容，请看[属性验证](/guide/components.html#Prop_Specification)。

#### 连字符特性的注解

HTML 特性名忽略大小写的区别，所以我们通常使用连字符属性代替驼峰形式。使用包含连字符特性的 `props` 的时候，有一些使用特殊情况：

1. 如果该特性是一个数据特性， `data-` 前缀会自动去掉；

2. 如果该属性仍然包含 - ，它会被驼峰化。这是因为在模板里访问包含 - 的最高层级属性不太方便：表达式 `my-param` 将会被解析为一个减法表达式，除非你使用不优雅的 `this['my-param']` 语法。

这意味着一个参数特性 `data-hello` 将会被重新设置在vm的 `vm.hello` 上；并且 `my-param` 会被设置为 `vm.myParam` 。

### methods

- **类型：** `Object`

Methods是被mixed到Vue实例. 你可以通过VM实例访问这些方法，或者在指令表达式里使用他们. 所有方法的`this`就是Vue实例本身.

**例子：**

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

Computed的属性是被mixed到Vue实例. 所有getters和setters的`this`就是Vue实例本身.

**例子：**

```js
var vm = new Vue({
  data: { a: 1 },
  computed: {
    // get only, just need a function
    aDouble: function () {
      return this.a * 2
    },
    // both get and set
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
- **限制：** 使用`Vue.extend()`时只接受`Function`类型。

提供一个包含存在的 DOM 元素的 Vue 实例。它可以是一个 CSS 选择器字符串，一个实际的 HTML 元素，或者是一个返回 HTML 元素的函数。注意到这个提供的元素仅仅充当一个挂载点；如果一个模板也被提供了，那么实例将会被替代，除非 `replace` 被设置为false。处理过的元素可以通过 `vm.$el` 访问。

当用`Vue.extend`，必须使用函数返回一个有效值，来保证每个实例得到一个独立的元素。

如果初始化的时候就提供了，那就马上进行编译；否则，只有执行了`vm.$mount()`才开始编译。

### template

- **类型：** `String`

一个字符串模板会被用作 Vue 实例的标记。默认情况下，该模板会 **替代** 挂载的元素。当 `replace` 选项被设置为 `false` ，这个模板将会被插入到挂载的元素内部。在两种情况下，挂载元素内部任何存在的标记都会被忽略，除非 [内容插入点](/guide/components.html#Content_Insertion) 存在模板里面。

如果它以`#`开头将会被当做(DOM)选择器处理，使用被选取元素的`innerHTML`和字符串模板。这样允许使用公共的`<script type="x-template">`方式包含模板。

注意到如果一个模板包含多余一个顶层节点，那么实例将会成为一个[碎片实例](/guide/best-practices.html#Fragment_Instance) - 也就是 一个管理节点列表而不是一个单独的节点。

<p class="tip">Vue.js使用基于DOM的模板体系。编译器走遍所有DOM元素去找指令描述来绑定数据。这就意味着所有的Vue.js模板都是可以转成浏览器可以识别的DOM元素。Vue.js转化字符串模板到DOM fragments，所以他们可以被复制在创建更多Vue实例的时候。如果你想你的模板是有效的HTML，你可以设置指令表达式的前缀是`data-`。</p>

### replace

- **类型：** `Boolean`  
- **缺省值：** `false`
- **限制：** 只有提供**template**选项的时候

是否用模板替换正在被挂载的元素。如果设置为 `false` ，该模板将会覆盖元素内部的内容，而不是替代元素本身。

**Example**:

``` html
<div id="replace"></div>
```

``` js
new Vue({
  el: '#replace',
  template: '<p>replaced</p>'
})
```

编译结果是：

``` html
<p>replaced</p>
```

相比之下, 当 `replace` 被设置成 `false`：

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

编译结果是：

``` html
<div id="insert">
  <p>inserted</p>
</div>
```

## Lifecycle

所有的生命周期的`this`都Vue实例。Vue实例也有对应的事件，以`"hook:<hookName>"`的形式。例如为`created`触发一个`hook:created`。

### created

- **类型：** `Function`

在实例被创建的时候同步调用。在这个阶段，实例完成了包含以下内容的预处理：数据健康，数据监控，计算属性，方法，监控事件回调。但DOM编译还没开始，`$el`还不可用。

### beforeCompile

- **类型：** `Function`

在编译之前调用。

### compiled

- **类型：** `Function`

编译完成后调用，在这个阶段，所有的指令都绑定，数据变化会触发DOM更新。但不能保证`$el`已经被插入到DOM中。

### ready

- **类型：** `Function`

当完成编译**而且** `$el` 第一次插入到DOM之后调用，也就是刚好在第一次 `attached` hook之后。注意这个插入必须要通过 Vue 完成的(例如 `vm.$appendTo()` 的方法或者是一个指令更新的结果)来触发的 `ready` 事件。

### attached

- **类型：** `Function`

当`vm.$el`被一个指令或是VM实例方法（例如`$appendTo()`）添加到DOM里的时候调用。直接操作`vm.$el`**不会**触发这个事件。

### detached

- **类型：** `Function`

当`vm.$el`被一个指令或是VM实例方法从DOM里删除的时候调用。直接操作`vm.$el`**不会**触发这个事件。

### beforeDestroy

- **类型：** `Function`

在一个Vue实例被销毁之前调用。这个时候，实例的绑定和指令仍工作正常。

### destroyed

- **类型：** `Function`

在一个Vue实例被销毁之后调用。如果被执行，所有的Vue实例的绑定和指令都会被解除绑定，所有子组件也会被销毁.

注意如果有一个leaving transition，`destroyed`被执行在transition结束**之后**.

## Assets

这里有一些Vue实例和它的子实例在编译期有效的私有的资源。

### directives

- **类型：** `Object`

一个指令的哈希表。参看[写自定义指令](../guide/custom-directive.html).

### elementDirectives

- **类型：** `Object`

一个元素级指令的哈希表。参看[元素指令](/guide/custom-directive.html#Element_Directives)。

### filters

- **类型：** `Object`

一个过滤器的哈希表。参看[写自定义过滤器](../guide/custom-filter.html).

### components

- **类型：** `Object`

一个组件的哈希表。参看[组件系统](../guide/components.html).

### transitions

- **类型：** `Object`

一个transition的哈希表。详细查看[过渡效果](../guide/transitions.html)。

### partials

- **Type:** `Object`

一个对实例可行的片段字符串的哈希值。更多细节请看[片段](/api/elements.html#partial)。

## Others

### inherit

- **类型：** `Boolean`
- **Default:** `false`

是否继承父组件的数据. 如果你想从父组件继承数据，就设成`true`。`inherit`是`true`的时候你可以：

1. 在当先组件模板里绑定父组件的数据属性；
2. 直接访问父组件的属性（通过prototypal继承）。

重要的是，当用`inherit: true`，**子组件也可以改变父组件的属性值**，因为所有Vue实例的数据都是getter/setters。

**例子：**

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
// the following line modifies parent.a
// instead of creating a new property on child:
child.a = 2
parent.a // -> 2
```

### events

Events对象的key是事件名，值就是相应的回调函数值。注意,这是Vue的事件不是DOM事件。值也可以是一个方法名。Vue实例会在初始化的时候对每一个events对象的属性执行`$on()`。

**例子：**

``` js
var vm = new Vue({
  events: {
    'hook:created': function () {
      console.log('created!')
    },
    greeting: function (msg) {
      console.log(msg)
    },
    // can also use a string for methods
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

一个键是监听的表达式且值是对应的回调的对象。值也可以是一个方法名的字符串，或者是一个包含额外选项的对象。 Vue 实例将会在初始化的时候对该对象的每个条目调用 `$watch()` 。

**例子：**

``` js
var vm = new Vue({
  data: {
    a: 1
  },
  watch: {
    'a': function (val, oldVal) {
      console.log('new: %s, old: %s', val, oldVal)
    },
    // string method name
    'b': 'someMethod',
    // deep watcher
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

`mixins` 接受一个mixin对象数组. 就像正常的实例对象一样，这些mixin对象包含实例选项，而且他们也会被合并到最终的选项。e.g. 如果你加了一个created hook ，那么这个组件就会执行所有的created hook。

Mixin hooks 会被按照他们提供的顺序调用，并且在容器本身的 hooks 之前。

**例子：**

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
- **限制:** 仅限使用 `Vue.extend()`的时候。

当在console里监视一个扩展过的Vue组件的时候，缺省构造函数名是 `VueComponent` ，但它并不是很有用。但你可以传一个可选项`name`到`Vue.extend()`，这样你就能知道你正在看哪个组件。这个字符串或被camelized并作为组件的构造函数的名字使用。

**例子：**

``` js
var Ctor = Vue.extend({
  name: 'cool-stuff'
})
var vm = new Ctor()
console.log(vm) // -> CoolStuff {$el: null, ...}
```

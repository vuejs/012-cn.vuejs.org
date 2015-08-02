title: 自定义指令
type: 教程
order: 9
---

## 基础

Vue.js 允许你注册自定义指令，实质上是让你教 Vue 一些新技巧：怎样将数据的变化映射到 DOM 的行为。你可以使用 `Vue.directive(id, definition)` 的方法传入**指令id**和**定义对象**来注册一个全局自定义指令。定义对象需要提供一些钩子函数（全部可选）：

- **bind**: 仅调用一次，当指令第一次绑定元素的时候。
-	**update**: 第一次是紧跟在 bind 之后调用，获得的参数是绑定的初始值；以后每当绑定的值发生变化就会被调用，获得新值与旧值两个参数。
-	**unbind**：仅调用一次，当指令解绑元素的时候。

**Example**

``` js
Vue.directive('my-directive', {
  bind: function () {
    // 做绑定的准备工作
    // 比如添加事件监听器，或是其他只需要执行一次的复杂操作
  },
  update: function (newValue, oldValue) {
    // 根据获得的新值执行对应的更新
    // 对于初始值也会被调用一次
  },
  unbind: function () {
    // 做清理工作
    // 比如移除在 bind() 中添加的事件监听器
  }
})
```

一旦注册好自定义指令，你就可以在 Vue.js 模板中像这样来使用它（需要添加 Vue.js 的指令前缀，默认为 `v-`）：

``` html
<div v-my-directive="someValue"></div>
```

如果你只需要 `update` 函数，你可以只传入一个函数，而不用传定义对象：

``` js
Vue.directive('my-directive', function (value) {
  // 这个函数会被作为 update() 函数使用
})
```

所有的钩子函数会被复制到实际的**指令对象**中，而这个指令对象将会是所有钩子函数的 `this`上下文环境。指令对象上暴露了一些有用的公开属性：

- **el**: 指令绑定的元素
- **vm**: 拥有该指令的上下文 ViewModel
- **expression**: 指令的表达式，不包括参数和过滤器
- **arg**: 指令的参数
- **raw**: 未被解析的原始表达式
- **name**: 不带前缀的指令名

<p class="tip">这些属性是只读的，不要修改它们。你也可以给指令对象附加自定义的属性，但是注意不要覆盖已有的内部属性。</p>

使用指令对象属性的示例：

``` html
<div id="demo" v-demo="LightSlateGray : msg"></div>
```

``` js
Vue.directive('demo', {
  bind: function () {
    this.el.style.color = '#fff'
    this.el.style.backgroundColor = this.arg
  },
  update: function (value) {
    this.el.innerHTML =
      'name - '       + this.name + '<br>' +
      'raw - '        + this.raw + '<br>' +
      'expression - ' + this.expression + '<br>' +
      'argument - '   + this.arg + '<br>' +
      'value - '      + value
  }
})
var demo = new Vue({
  el: '#demo',
  data: {
    msg: 'hello!'
  }
})
```

**Result**

<div id="demo" v-demo="LightSlateGray : msg"></div>
<script>
Vue.directive('demo', {
  bind: function () {
    this.el.style.color = '#fff'
    this.el.style.backgroundColor = this.arg
  },
  update: function (value) {
    this.el.innerHTML =
      'name - ' + this.name + '<br>' +
      'raw - ' + this.raw + '<br>' +
      'expression - ' + this.expression + '<br>' +
      'argument - ' + this.arg + '<br>' +
      'value - ' + value
  }
})
var demo = new Vue({
  el: '#demo',
  data: {
    msg: 'hello!'
  }
})
</script>

### 多重从句

同一个特性内部，逗号分隔的多个从句将被绑定为多个指令实例。在下面的例子中，指令会被创建和调用两次：

``` html
<div v-demo="color: 'white', text: 'hello!'"></div>
```

如果想要用单个指令实例处理多个参数，可以利用字面量对象作为表达式：

``` html
<div v-demo="{color: 'white', text: 'hello!'}"></div>
```

``` js
Vue.directive('demo', function (value) {
  console.log(value) // Object {color: 'white', text: 'hello!'}
})
```

## 字面指令

如果在创建自定义指令的时候传入 `isLiteral: true` ，那么特性值就会被看成直接字符串，并被赋值给该指令的 `expression`。字面指令不会试图建立数据监视。

Example:

``` html
<div v-literal-dir="foo"></div>
```

``` js
Vue.directive('literal-dir', {
  isLiteral: true,
  bind: function () {
    console.log(this.expression) // 'foo'
  }
})
```

### 动态字面指令

然而，在字面指令含有 Mustache 标签的情形下，指令的行为如下：

- 指令实例会有一个属性，`this._isDynamicLiteral` 被设为 `true`；

- 如果没有提供 `update` 函数，Mustache 表达式只会被求值一次，并将该值赋给 `this.expression` 。不会对表达式进行数据监视。

- 如果提供了 `update` 函数，指令**将**会为表达式建立一个数据监视，并且在计算结果变化的时候调用 `update`。

## 双向指令

如果你的指令想向 Vue 实例写回数据，你需要传入 `twoWay: true` 。该选项允许在指令中使用 `this.set(value)`。

``` js
Vue.directive('example', {
  twoWay: true,
  bind: function () {
    this.handler = function () {
      // 把数据写回 vm
      // 如果指令这样绑定 v-example="a.b.c",
      // 这里将会给 `vm.a.b.c` 赋值
      this.set(this.el.value)
    }.bind(this)
    this.el.addEventListener('input', this.handler)
  },
  unbind: function () {
    this.el.removeEventListener('input', this.handler)
  }
})
```

## 内联语句

传入 `acceptStatement: true` 可以让自定义指令像 `v-on` 一样接受内联语句：

``` html
<div v-my-directive="a++"></div>
```

``` js
Vue.directive('my-directive', {
  acceptStatement: true,
  update: function (fn) {
    // the passed in value is a function which when called,
    // will execute the "a++" statement in the owner vm's
    // scope.
  }
})
```

但是请明智地使用此功能，因为通常我们希望避免在模板中产生副作用。

## 深度数据观察

如果你希望在一个对象上使用自定义指令，并且当对象内部嵌套的属性发生变化时也能够触发指令的 `update` 函数，那么你就要在指令的定义中传入 `deep: true`。

``` html
<div v-my-directive="obj"></div>
```

``` js
Vue.directive('my-directive', {
  deep: true,
  update: function (obj) {
    // 当 obj 内部嵌套的属性变化时也会调用此函数
  }
})
```

## 指令优先级

你可以选择给指令提供一个优先级数（默认是0）。同一个元素上优先级越高的指令会比其他的指令处理得早一些。优先级一样的指令会按照其在元素特性列表中出现的顺序依次处理，但是不能保证这个顺序在不同的浏览器中是一致的。

通常来说作为用户，你并不需要关心内置指令的优先级，如果你感兴趣的话，可以参阅源码。逻辑控制指令 `v-repeat`, `v-if` 被视为 “终结性指令”，它们在编译过程中始终拥有最高的优先级。

## 元素指令

有时候，我们可能想要我们的指令可以以自定义元素的形式被使用，而不是作为一个特性。这与 Angular 的 `E` 类指令的概念非常相似。元素指令可以看做是一个轻量的自定义组件（后面会讲到）。你可以像下面这样注册一个自定义的元素指令：

``` js
Vue.elementDirective('my-directive', {
  // 和普通指令的 API 一致
  bind: function () {
    // 对 this.el 进行操作...
  }
})
```

使用时我们不再用这样的写法:

``` html
<div v-my-directive></div>
```

而是写成:

``` html
<my-directive></my-directive>
```

元素指令不能接受参数或表达式，但是它可以读取元素的特性，来决定它的行为。

与通常的指令有个很大的不同，元素指令是**终结性**的，这意味着，一旦 Vue 遇到一个元素指令，它将跳过对该元素和其子元素的编译 - 即只有该元素指令本身可以操作该元素及其子元素。

下面，我们来看怎样写一个 [自定义过滤器](../guide/custom-filter.html)。

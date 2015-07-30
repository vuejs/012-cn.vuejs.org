title: 组件系统
type: guide
order: 11
---

## 使用组件

Vue.js 支持把扩展而来的 Vue 的子类用作概念上与 [Web Components](http://www.w3.org/TR/components-intro/) 类似的可复用组件，无需任何 polyfill。要创建组件，只需借助 `Vue.extend()` 创建一个 Vue 的子类构造函数：

``` js
// 扩展 Vue 得到一个可复用的构造函数
var MyComponent = Vue.extend({
  template: '<p>A custom component!</p>'
})
```

Vue 的构造函数可接收的大部分选项都能在 `Vue.extend()` 中使用，不过也有两个特例：`data` 和 `el`。由于每个 Vue 的实例都应该有自己的 `$data` 和 `$el`，我们显然不希望传递给 `Vue.extend()` 的值被所有通过这个构造函数创建的实例所共享。因此如果要定义组件初始化默认数据和元素的方式，应该传入一个函数：

``` js
var ComponentWithDefaultData = Vue.extend({
  data: function () {
    return {
      title: 'Hello!'
    }
  }
})
```

接下来，就可以用 `Vue.component()` 来**注册**这个构造函数了：

``` js
// 把构造函数注册到 my-component 这个 id
Vue.component('my-component', MyComponent)
```

为了更简单，也可以直接传入 option 对象来代替构造函数。如果接收到的是一个对象，`Vue.component()` 会为你隐式调用 `Vue.extend()`：

``` js
// 注意：该方法返回全局 Vue 对象，
// 而非注册的构造函数
Vue.component('my-component', {
  template: '<p>A custom component!</p>'
})
```

之后就能在父级实例的模板中使用注册过的组件了 (确保在初始化根实例**之前**已经注册了组件) ：

``` html
<!-- 父级模板 -->
<my-component></my-component>
```

会渲染成：

``` html
<p>A custom component!</p>
```

你无需在全局注册所有组件。你可以限制一个组件仅对另一个组件及其后代可用，只要在 `components` 选项中传入这个组件即可 (这种封装形式同样适用于其他资源，例如指令和过滤器) ：

``` js
var Parent = Vue.extend({
  components: {
    child: {
      // child 只能被
      // Parent 及其后代组件使用
    }
  }
})
```

理解 `Vue.extend()` 和 `Vue.component()` 的区别至关重要。由于 `Vue` 本身是一个构造函数， `Vue.extend()` 是一个**类继承方法**。它用来创建一个 `Vue` 的子类并返回其构造函数。而另一方面，`Vue.component()` 是一个类似 `Vue.directive()` 和 `Vue.filter()` 的**资源注册方法**。它作用是建立指定的构造函数与 ID 字符串间的关系，从而让 Vue.js 能在模板中使用它。直接向 `Vue.component()` 传递 options 时，它会在内部调用 `Vue.extend()`。

Vue.js 支持两种不同风格的调用组件的 API：命令式的基于构造函数的 API，以及基于模板的声明式的 Web Components 风格 API。如果你感到困惑，想一下通过 `new Image()` 和通过 `<img>` 标签这两种创建图片元素的方式。它们都在各自的适用场景下发挥着作用，为了尽可能灵活，Vue.js 同时提供这两种方式。

<p class="tip">`table` 元素对能出现在其内部的元素类型有限制，因此自定义元素会被提到外部而且无法正常渲染。在那种情况下你可以使用组件命令式语法： `<tr v-component="my-component"></tr>`。</p>

## 数据流

### 通过 prop 传递数据

默认情况下，组件有**独立作用域**。这意味着你无法在子组件的模板中引用父级的数据。为了传递数据到拥有独立作用域的子组件中，我们需要用到 `prop`。

一个“prop”是指组件的数据对象上的一个预期会从父级组件取得的字段。一个子组件需要通过 [`prop` 选项](/api/options.html#props)显式声明它希望获得的 prop：

``` js
Vue.component('child', {
  // 声明 prop
  props: ['msg'],
  // prop 可以在模板内部被使用，
  // 也可以类似 `this.msg` 这样来赋值
  template: '<span>{{msg}}</span>'
})
```

然后，我们可以像这样向这个组件传递数据：

``` html
<child msg="hello!"></child>
```

**结果：**

<div id="prop-example-1" class="demo"><child msg="hello!"></child></div>
<script>
new Vue({
  el: '#prop-example-1',
  components: {
    child: {
      props: ['msg'],
      template: '<span>{&#123;msg&#125;}</span>'
    }
  }
})
</script>

### 驼峰 vs. 连字符

HTML 特性是大小写不敏感的。当驼峰式的 prop 名用于特性时，你需要用下划线形式代替：

``` js
Vue.component('child', {
  props: ['myMessage'],
  template: '<span>{{myMessage}}</span>'
})
```

``` html
<!-- 重要：使用横线分隔的名称！ -->
<child my-message="hello!"></child>
```

### 动态 prop

我们同样能够从父级向下传递动态数据。例如：

``` html
<div>
  <input v-model="parentMsg">
  <br>
  <child msg="{{parentMsg}}"></child>
</div>
```

**结果:**

<div id="demo-2" class="demo"><input v-model="parentMsg"><br><child msg="{&#123;parentMsg&#125;}"></child></div>
<script>
new Vue({
  el: '#demo-2',
  data: {
    parentMsg: 'Inherited message'
  },
  components: {
    child: {
      props: ['msg'],
      template: '<span>{&#123;msg&#125;}</span>'
    }
  }
})
</script>

<p class="tip">暴露 `$data` 作为 prop 也是可行的。传入的值必须是一个对象，它会被用来替换组件默认的 `$data` 对象。</p>

### 传递回调作为 prop

同样可以向下传递一个方法或语句作为子组件的一个回调方法。从而可以进行命令式的、解耦的父级—子级通信：

``` js
Vue.component('parent', {
  // ...
  methods: {
    onChildLoaded: function (msg) {
      console.log(msg)
    }
  }
})

Vue.component('child', {
  // ...
  props: ['onLoad'],
  ready: function () {
    this.onLoad('message from child!')
  }
})
```

``` html
<!-- 父级模板 -->
<child on-load="{{onChildLoaded}}"></child>
```

### prop 绑定类型

默认情况下，所有 prop 都会在子级和父级的属性之间建立一个**单向向下传递**的绑定关系：当父级的属性更新时，它将向下同步至子级，反之则不会。这种默认设定是为了防止子级组件意外篡改父级的状态，那将很难推导应用的数据流。不过也可以显式指定一个双向或者一次性的绑定：

对比这些语法：

``` html
<!-- 默认情况下，单向绑定 -->
<child msg="{{parentMsg}}"></child>
<!-- 显式双向绑定 -->
<child msg="{{@ parentMsg}}"></child>
<!-- 显示一次性绑定 -->
<child msg="{{* parentMsg}}"></child>
```

双向绑定会反向同步子级的 `msg` 属性到父级的 `parentMsg` 属性。一次性绑定在完成之后不会在父级和子级之间同步未来发生的变化。

<p class="tip">注意如果传递的 prop 值是对象或数组，将会是引用传递。在子级改动对象或数组将会影响到父级的状态，这种情况会无视你使用的绑定的类型。</p>

### prop 规则

组件可以对接收的 prop 限定规则。在开发给他人使用的组件时这会很有用，由于对 prop 的有效性检验是组件 API 的重要组成部分，并且能保证用户正确地使用了组件。与直接把 prop 定义成字符串不同，你可以使用包含有效性要求的对象：

``` js
Vue.component('example', {
  props: {
    // 基本检查 (`null` 表示接受所有类型)
    onSomeEvent: Function,
    // 存在性检查
    requiredProp: {
      type: String,
      required: true
    },
    // 指定默认值
    propWithDefault: {
      type: Number,
      default: 100
    },
    // 对象或数组类型的默认值
    // 应该由工厂函数返回
    propWithObjectDefault: {
      type: Object,
      default: function () {
        return { msg: 'hello' }
      }
    },
    // 双向 prop。
    // 如果绑定类型不匹配将抛出警告.
    twoWayProp: {
      twoWay: true
    },
    // 自定义验证函数
    greaterThanTen: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

其中 `type` 可以是以下任一本地构造函数：

- String
- Number
- Boolean
- Function
- Object
- Array

另外，`type` 还可以是自定义构造函数，断言将会是一个 `instanceof` 检查。

如果 prop 检验不通过，Vue 会拒绝这次针对子组件的赋值，并且在使用开发版本时会抛出一个警告。

### 继承父级作用域

如果有需要，你也可以使用 `inherit: true` 选项来让子组件通过原型链继承父级的全部属性：

``` js
var parent = new Vue({
  data: {
    a: 1
  }
})
// $addChild() 是一个实例方法，
// 它允许你用代码创建子实例。
var child = parent.$addChild({
  inherit: true,
  data: {
    b: 2
  }
})
console.log(child.a) // -> 1
console.log(child.b) // -> 2
parent.a = 3
console.log(child.a) // -> 3
```

这里有一点需要注意：由于 Vue 实例上的数据属性都是 getter/setter，设置 `child.a = 2` 会直接改变 `parent.a` 的值，而非在子级创建一个新属性遮蔽父级中的属性：

``` js
child.a = 4
console.log(parent.a) // -> 4
console.log(child.hasOwnProperty('a')) // -> false
```

### 作用域注意事项

当组件被用在父模板中时，例如：

``` html
<!-- 父模板 -->
<my-component v-show="active" v-on="click:onClick"></my-component>
```

这里的命令 (`v-show` 和 `v-on`) 会在父作用域编译，所以 `active` 和 `onClick` 的取值取决于父级。任何子模版中的命令和插值都会在子作用域中编译。这样使得上下级组件间更好地分离。

阅读 [组件作用域](/guide/best-practices.html#Component_Scope) 了解更多细节。

## 组件生命周期

每一个组件，或者说 Vue 的实例，都有着自己的生命周期：它会被创建、编译、附加、分离，最终销毁。在这每一个关键点，实例都会触发相应的事件，而在创建实例或者定义组件时，我们可以传入生命周期钩子函数来响应这些事件。例如：

``` js
var MyComponent = Vue.extend({
  created: function () {
    console.log('An instance of MyComponent has been created!')
  }
})
```

查阅 API 文档中可用的 [生命周期钩子函数完整列表](../api/options.html#Lifecycle)。

## 动态组件

你可以使用保留的 `<component>` 元素在组件间动态切换来实现“页面切换”：

``` js
new Vue({
  el: 'body',
  data: {
    currentView: 'home'
  },
  components: {
    home: { /* ... */ },
    posts: { /* ... */ },
    archive: { /* ... */ }
  }
})
```

``` html
<component is="{{currentView}}">
  <!-- 内容随 vm.currentview 一同改变！ -->
</component>
```

如果希望被切换出去的组件保持存活，从而保留它的当前状态或者避免反复重新渲染，你可以加上 `keep-alive` 命令参数：

``` html
<component is="{{currentView}}" keep-alive>
  <!-- 不活跃的的组件会被缓存！ -->
</component>
```

## 过渡控制

有两个额外的特性参数能够支持对需要渲染或过渡的组件进行高级控制。

### `wait-for` 等待事件

等待即将进入的组件触发该事件后再插入 DOM。这就允许你等待数据异步加载完成后再触发过渡，避免显示空白内容。

这一特性可以用于静态和动态组件。注意：对于动态组件，所有有待渲染的组件都必须通过 `$emit` 触发指定事件，否则他们永远不会被插入。

**示例：**

``` html
<!-- 静态组件 -->
<my-component wait-for="data-loaded"></my-component>

<!-- 动态组件 -->
<component is="{{view}}" wait-for="data-loaded"></component>
```

``` js
// 组件定义
{
  // 获取数据并在编译完成钩子函数中异步触发事件。
  // 这里jQuery只是用作演示。
  compiled: function () {
    var self = this
    $.ajax({
      // ...
      success: function (data) {
        self.$data = data
        self.$emit('data-loaded')
      }
    })
  }
}
```

### `transition-mode` 过渡模式

`transition-mode` 特性参数允许指定两个动态组件之间的过渡如何进行。

默认情况下，进入组件和退出组件的过渡是同时进行的。这个特性参数允许设置成另外两种模式：

- `in-out`：先进后出；先执行新组件过渡，当前组件在新组件过渡结束后执行过渡并退出。
- `out-in`：先出后进；当前组件首先执行过渡并退出，新组件在当前组件过渡结束后执行过渡并进入。

**示例：**

``` html
<!-- 先淡出，之后淡入 -->
<component
  is="{{view}}"
  v-transition="fade"
  transition-mode="out-in">
</component>
```

## 列表与组件

对于一个对象数组，你可以把组件和 `v-repeat` 组合使用。这种场景下，对于数组中的每个对象，都以该对象为 `$data` 创建一个子组件，以指定组件作为构造函数。

``` html
<ul id="list-example">
  <user-profile v-repeat="users"></user-profile>
</ul>
```

``` js
new Vue({
  el: '#list-example',
  data: {
    users: [
      {
        name: 'Chuck Norris',
        email: 'chuck@norris.com'
      },
      {
        name: 'Bruce Lee',
        email: 'bruce@lee.com'
      }
    ]
  },
  components: {
    'user-profile': {
      template: '<li>{{name}} {{email}}</li>'
    }
  }
})
```

**结果：**

<ul id="list-example" class="demo"><user-profile v-repeat="users"></user-profile></ul>
<script>
var parent2 = new Vue({
  el: '#list-example',
  data: {
    users: [
      {
        name: 'Chuck Norris',
        email: 'chuck@norris.com'
      },
      {
        name: 'Bruce Lee',
        email: 'bruce@lee.com'
      }
    ]
  },
  components: {
    'user-profile': {
      template: '<li>{&#123;name&#125;} - {&#123;email&#125;}</li>'
    }
  }
})
</script>

### 在组件循环中使用标识符

在组件中标识符语法同样适用，并且被循环的数据会被设置成组件的一个属性，以标识符作为键名：

``` html
<ul id="list-example">
  <!-- 在组件内部可以通过 `this.user` 访问数据 -->
  <user-profile v-repeat="user in users"></user-profile>
</ul>
```

<p class="tip">注意一旦组件跟 `v-repeat` 一同使用，同样的作用域规则也会被应用到该组件容器上的其他命令。结果就是，你在父级模板中将获取不到 `$index`；只能在组件自身的模板中获取。<br><br>或者，你也可以通过循环 `<template>` 来建立一个媒介作用域，但是大多数情况下在组件内部使用 `$index` 是更好的实践。</p>

## 子组件引用

某些情况下需要通过 JavaScript 访问嵌套的子组件。要实现这种操作，需要使用 `v-ref` 为子组件分配一个 ID。例如：

``` html
<div id="parent">
  <user-profile v-ref="profile"></user-profile>
</div>
```

``` js
var parent = new Vue({ el: '#parent' })
// 访问子组件
var child = parent.$.profile
```

当 `v-ref` 与 `v-repeat` 一同使用时，会获得一个与数据数组对应的子组件数组。

## 事件系统

虽然你可以直接访问一个 Vue 实例的子级与父级，但是通过内建的事件系统进行跨组件通讯更为便捷。这还能使你的代码进一步解耦，变得更易于维护。一旦建立了上下级关系，就能使用组件的 [事件实例方法](../api/instance-methods.html#Events) 来分发和触发事件。

``` js
var parent = new Vue({
  template: '<div><child></child></div>',
  created: function () {
    this.$on('child-created', function (child) {
      console.log('new child created: ')
      console.log(child)
    })
  },
  components: {
    child: {
      created: function () {
        this.$dispatch('child-created', this)
      }
    }
  }
}).$mount()
```

<script>
var parent = new Vue({
  template: '<div><child></child></div>',
  created: function () {
    this.$on('child-created', function (child) {
      console.log('new child created: ')
      console.log(child)
    })
  },
  components: {
    child: {
      created: function () {
        this.$dispatch('child-created', this)
      }
    }
  }
}).$mount()
</script>

## 私有资源

有时一个组件需要使用类似命令、过滤器和子组件这样的资源，但是又希望把这些资源封装起来以便自己在别处复用。这一点可以用私有资源实例化选项来实现。私有资源只能被拥有该资源的组件的实例及其子组件访问。

``` js
// 全部5种类型的资源
var MyComponent = Vue.extend({
  directives: {
    // “id : 定义”键值对，与处理全局方法的方式相同
    'private-directive': function () {
      // ...
    }
  },
  filters: {
    // ...
  },
  components: {
    // ...
  },
  partials: {
    // ...
  },
  effects: {
    // ...
  }
})
```

又或者，可以用与全局资源注册方法类似的链式 API 为现有组件构造方法添加私有资源：

``` js
MyComponent
  .directive('...', {})
  .filter('...', function () {})
  .component('...', {})
  // ...
```

## 内容插入

在创建可复用组件时，我们通常需要在宿主元素中访问和重用原始数据，而它们并非组件的一部分 (类似 Angular 的“transclusion”概念) 。 Vue.js 实现了一套内容插入机制，它和目前的 Web Components 规范草案兼容，使用特殊的 `<content>` 元素作为原始内容的插入点。

<p class="tip">**关键提示**：transclude 的内容会在父级作用域中编译，而非子级作用域。</p>

### 单插入点

只有一个不带特性的 `<content>` 标签时，整个原始内容都会被插入到它在 DOM 中的位置并把它替换掉。原来在 `<content>` 标签内部的所有内容会被视为 **后备内容**。后备内容只有在宿主元素为空且没有要插入的内容时才会被显示。例如：

`my-component` 的模板：

``` html
<h1>This is my component!</h1>
<content>This will only be displayed if no content is inserted</content>
```

使用该组件的父标签：

``` html
<my-component>
  <p>This is some original content</p>
  <p>This is some more original content</p>
</my-component>
```

渲染结果如下：

``` html
<my-component>
  <h1>This is my component!</h1>
  <p>This is some original content</p>
  <p>This is some more original content</p>
</my-component>
```

### 多插入点

`<content>` 元素有一个特殊特性 `select`，需要赋值为一个 CSS 选择器。可以使用多个包含不同 `select` 特性的 `<content>` 插入点，它们会被原始内容中与选择器匹配的部分所替代。

<p class="tip">从 0.11.6 起，`<content>` 选择器只能匹配宿主节点的顶级子节点。从而表现与 Shaddow DOM 规范一致，并且可以避免意外地选中嵌套的 transclude 内容中不需要的节点。</p>

举例来说，假设有一个带有如下模板的 `multi-insertion` 组件：

``` html
<content select="p:nth-child(3)"></content>
<content select="p:nth-child(2)"></content>
<content select="p:nth-child(1)"></content>
```

父标签：

``` html
<multi-insertion>
  <p>One</p>
  <p>Two</p>
  <p>Three</p>
</multi-insertion>
```

渲染结果如下：

``` html
<multi-insertion>
  <p>Three</p>
  <p>Two</p>
  <p>One</p>
</multi-insertion>
```

内容插入机制能很好地控制对原始内容的操作和显示，使组件极为灵活多变易于组合。

## 行内模板

在 0.11.6 中，为组件引入了一个特殊的特性参数： `inline-template`。当传递了这个参数时，组件会使用自己内部的内容作为模板而非 transclude 的内容。这会使模板编写更灵活。

``` html
<my-component inline-template>
  <p>These are compiled as the component's own template</p>
  <p>Not parent's transclusion content.</p>
</my-component>
```

## 异步组件

<p class="tip">异步组件只在 Vue ^0.12.0 版本中支持。</p>

在大型项目中，我们可能需要把应用分割成小的组成部分，并且只在实际用到一个组件的时候加载它。为了让这种操作更容易，Vue.js 允许把组件定义成一个异步确定组件定义的工厂方法。Vue.js 只会在需要渲染该组件时才触发相应的工厂方法，并且为了之后能再次渲染会缓存结果。例如：

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

工厂方法会收到一个 `resolve` 回调方法，应该在从服务器获得组件定义之后调用它。你也可以通过调用 `reject(reason)` 来提示加载失败。这里的 `setTimeout` 只是用作简单的演示；具体如何获取组件完全取决于你。有一种不错的手段是把异步组件和 [Webpack 的分离功能](http://webpack.github.io/docs/code-splitting.html)结合使用：

``` js
Vue.component('async-webpack-example', function (resolve) {
  // 这个特殊的 require 语法会通知 webpack
  // 自动把构建后的代码分割成
  // 会通过 ajax 请求自动加载的 bundle。
  require(['./my-async-component'], resolve)
})
```

下一节：[过渡效果](../guide/transitions.html)

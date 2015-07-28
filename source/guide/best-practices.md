title: 提示和最佳实践
type: guide
order: 15
is_new: true
---

## 数据初始化

定义确切的数据更加适合 Vue 的数据观察模式。建议，定义的时候，在 `data` 选项中初始化所有需要流动的属性。例如，给定下面的模版：


``` html
<div id="demo">
  <p v-class="green: validation.valid">{{message}}</p>
  <input v-model="message">
</div>
```

建议像这样初始化你的属性，而不是为空对象：

``` js
new Vue({
  el: '#demo',
  data: {
    message: '',
    validation: {
      valid: false
    }
  }
})
```

这样做的理由是， Vue 会不断地观察属性值的变化，使用 `Object.defineProperty` 转化当前存在的属性值到各个流动的 getters 和 setters 中。当 Vue 对象创建时，这个属性不存在，这个属性就不会被追踪观察。

但是你不需要在数据项中设置每一个嵌套属性。可以在初始化的时候将一个属性置为空对象，然后在后面的操作中设置为一个新的拥有嵌套的对象，这么做的理由是 Vue 依然会对这个属性进行追踪观察。

## 添加和删除属性

正如前面所说的， Vue 会使用 `Object.defineProperty` 通过转化属性值来观察数据。不过，在 ECMAScript 5 中，当一个新的属性被添加到对象或者从对象中删除的时候，不存在一种方法对属性进行检查。为了解决这个约束，观察的对象新添加了这三种方法：

- `obj.$add(key, value)`
- `obj.$set(key, value)`
- `obj.$delete(key)`

这些方法的用途是，从观察对象中去添加或者删除属性，然后触发所期待的 DOM 更新。 `$add` 和 `set` 的区别是假如这个 key 在对象里面， `$add` 会返回值，所以当使用 `$obj.$add(key)` 的时候不会覆盖存在的值变为 `undefined` 。

一个需要注意的一点是，当你通过索引来设置改变一个数据绑定的数组(e.g. `arr[1] = value`)， Vue 不会检查到改变。需要再次声明的是，你可以使用扩展的方法去通知 Vue.js 响应这些改变。被观察的数组有两个扩展的方法：

- `arr.$set(index, value)`
- `arr.$remove(index | value)`


Vue组件实例也有相应的实例方法：

- `vm.$get(path)`
- `vm.$set(path, value)`
- `vm.$add(key, value)`
- `vm.$delete(key, value)`

注意 `vm.$get` 和 `vm.set` 都接受路径。

<p class="tip">
尽管存在这些方法，必要的时候最好去添加需要观察的属性。为了理解你的组件状态，想象 <code>data</code> 选项为一个 schema 很有帮助。清晰地列出一个组建中所有可能存在的属性，当你后来去看这个组建的时候，可以更容易去理解这一个组建里面包含什么。</p>

## 理解异步更新

需要知道，默认情况下， Vue 表现的页面数据更新是异步的。无论数据在什么时候改变， Vue 都会打开一个队列，然后把所有变化的数据缓存在一个相同的事件循环当中。假如一个观察事件在一个事件循环中被触发了多次，只会 push 到队列中一次。然后，在下一次的事件循环发生之时， Vue 会清除队列然后表现出需要的 DOM 更新。假如可以用于异步队列，在内部， Vue 会使用 `MutationObserver` ，然后触发 `setTimeout(fn, 0)` 。

例如，当你设置 `vm.someData = 'new value'` ， DOM 不会马上更新，当队列被清除，它将会在下一个事件循环的触发时更新。当你想要根据更新的 DOM 状态去做某些事情，这个细节需要被注意到。尽管 Vue.js 鼓励开发者用数据驱动的方式想问题，避免直接操作 DOM ，有时候你仅仅想要去使用简单的经常使用的 jQuery 插件。在数据改变后，直到 Vue.js 完成更新 DOM ，在这个等待过程中，你可以马上使用 `Vue.nextTick(callback)` ，当回调函数执行时，DOM会被更新。例如：

``` html
<div id="example">{{msg}}</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    msg: '123'
  }
})
vm.msg = 'new message' // change data
vm.$el.textContent === 'new message' // false
Vue.nextTick(function () {
  vm.$el.textContent === 'new message' // true
})
```
这与 `vm.$nextTick()` 实例方法相同，很方便内部的组建使用，因为这不需要全局的 `Vue` ，回调函数的 `this` 上下文会自动绑定到当前的Vue实例：

``` js
Vue.component('example', {
  template: '<span>{{msg}}</span>',
  data: function () {
    return {
      msg: 'not updated'
    }
  },
  methods: {
    updateMessage: function () {
      this.msg = 'updated'
      console.log(this.$el.textContent) // => 'not updated'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => 'updated'
      })
    }
  }
})
```

## 组件作用域

每一个 Vue.js 组件是一个分开的拥有自己作用域 Vue 实例对象。当用到组建的时候，很重要去认识到作用域怎么工作。经验法则是：

> 假如有东西在父模版中，它会编译在父模版中；假如它出现在子模版中，它会编译在子模版中。

一个常见的错误是，在父模版中，尝试去绑定一个指令到一个子模版的属性或者方法：


``` html
<div id="demo">
  <!-- does NOT work -->
  <child-component v-on="click: childMethod"></child-component>
</div>
```

<<<<<<< HEAD
假如你想要去绑定子模版的指令到模版根节点，你应该使用 `replace: true` ，然后在子模版中包含根节点：
=======
If you need to bind child-scope directives on a component root node, you should do so in the child component's own template:
>>>>>>> 0.12.8

``` js
Vue.component('child-component', {
  // this does work, because we are in the right scope
  template: '<div v-on="click: childMethod">Child</div>',
  methods: {
    childMethod: function () {
      console.log('child method invoked!')
    }
  }
})
```
注意，当在组件中使用 `v-repeat` 时，这个行为也可应用到 `$index` 。

类似地，组件内部的HTML内容被认为是"嵌入内容"。他们不会插入在任何地方，除非子模版包含至少一个 '<content></content>' 出口。这个被插入的内容也是被编译到父作用域中：

``` html
<div>
  <child-component>
    <!-- compiled in parent scope -->
    <p>{{msg}}</p>
  </child-component>
</div>
```
你可以使用 `inline-template` 属性去明确内容在子模版的作用域中被编译：

``` html
<div>
  <child-component inline-template>
    <!-- compiled in child scope -->
    <p>{{msg}}</p>
  </child-component>
</div>
```
更多的细节，请看 [Content Insertion](/guide/components.html#Content_Insertion)。

## 在多个实例之间通讯

在 Vue 中进行父子模版通讯，常见的一种做法是，通过 `props` 传递一个父方法作为一个回调方法到子模版。这样允许通讯的内容可以被定义在模版中（父模版定义子模版的地方），这保持了 Javascript 细节之间的解耦：

``` html
<div id="demo">
  <p>Child says: {{msg}}</p>
  <child-component send-message="{{onChildMsg}}"></child-component>
</div>
```

``` js
new Vue({
  el: '#demo',
  data: {
    msg: ''
  },
  methods: {
    onChildMsg: function(msg) {
      this.msg = msg
      return 'Got it!'
    }
  },
  components: {
    'child-component': {
      props: [
        // you can use prop assertions to ensure the
        // callback prop is indeed a function.
        {
          name: 'send-message',
          type: Function,
          required: true
        }
      ],
      // props with hyphens are auto-camelized
      template:
        '<button v-on="click:onClick">Say Yeah!</button>' +
        '<p>Parent responds: {{response}}</p>',
      // component `data` option must be a function
      data: function () {
        return {
          response: ''
        }
      },
      methods: {
        onClick: function () {
          this.response = this.sendMessage('Yeah!')
        }
      }
    }
  }
})
```

**Result:**

<div id="demo"><p>Child says: {&#123;msg&#125;}</p><child-component send-message="{&#123;onChildMsg&#125;}"></child-component></div>

<script>
new Vue({
  el: '#demo',
  data: {
    msg: ''
  },
  methods: {
    onChildMsg: function(msg) {
      this.msg = msg
      return 'Got it!'
    }
  },
  components: {
    'child-component': {
      props: ['send-message'],
      data: function () {
        return {
          fromParent: ''
        }
      },
      template:
        '<button v-on="click:onClick">Say Yeah!</button>' +
        '<p>Parent responds: <span v-text="fromParent"></span></p>',
      methods: {
        onClick: function () {
          this.fromParent = this.sendMessage('Yeah!')
        }
      }
    }
  }
})
</script>

当你需要跨越几个嵌套的组建中通讯时，你可以使用 [Event System](/api/instance-methods.html#Events) 。另外，在构建大型应用的情况下，在Vue中使用 [Flux](https://facebook.github.io/flux/docs/overview.html)-like 架构也是可行的。


## Prop 的使用方法

假如你曾经尝试去在 `created` hook 方法中使用一个组件的属性或方法，你会发现会出现 `undefined` 的情况。这是因为 `created` hook 的触发，是实例在任何 DOM 编译发生之前，所以属性还不会被处理和定义。在模版编译之后，属性和方法会使用父模版的属性和方法初始化。相似地，在编译之后，双向绑定属性和方法只能触发父模版的变化。

<<<<<<< HEAD

## 改变默认选项
=======
## Fragment Instance

Starting in 0.12.2, the `replace` option now defaults to `true`. This basically means:

> Whatever you put in the template will be what ends up rendered in the DOM.

So, if your template contains more than one top-level elements:

``` js
Vue.component('example', {
  template:
    '<div>A</div>' +
    '<div>B</div>'
})
```

Or, if the template contains only text:

``` js
Vue.component('example', {
  template: 'Hello world'
})
```

In both cases, the instance will become a **fragment instance** which doesn't have a root element. A fragment instance's `$el` will point to an "anchor node", which is an empty Text node (or a Comment node in debug mode). What's probably more important though, is that directives, transitions and attributes (except for props) on the component element will not take any effect - because there is no root element to bind them to:

``` html
<!-- doesn't work due to no root element -->
<example v-show="ok" v-transition="fade"></example>

<!-- props work as intended -->
<example prop="{{someData}}"></example>
```

There are, of course, valid use cases for fragment instances, but it is in general a good idea to give your component template a single root element. It ensures directives and attributes on the component element to be properly transferred, and also results in slightly better performance.
>>>>>>> 0.12.8

可以通过设置 `Vue.options` ，去改变选项的默认值。例如，你可以设置 `Vue.options.replace = true` ，使所有 Vue 实例变量都表现出 `replace: true` 。小心使用这个特性，最好当你开始一个新项目的时候使用它，因为它会影响所有 Vue 实例变量的行为。

<<<<<<< HEAD
=======
It is possible to change the default value of an option by setting it on the global `Vue.options` object. For example, you can set `Vue.options.replace = false` to give all Vue instances the behavior of `replace: false`. Use this feature carefully, and use it only when you are starting a new project, because it affects the behavior of every instance.
>>>>>>> 0.12.8

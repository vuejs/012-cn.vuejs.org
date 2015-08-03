title: 细节与最佳实践
type: 教程
order: 15
is_new: true
---

## 数据初始化

明确定义的数据模型更加适合 Vue 的数据观察模式。建议在定义组件时，在 `data` 选项中初始化所有需要进行动态观察的属性。例如，给定下面的模版：


``` html
<div id="demo">
  <p v-class="green: validation.valid">{{message}}</p>
  <input v-model="message">
</div>
```

建议像这样初始化你的数据，而不是什么都不定义：

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

为什么要这样做呢？因为 Vue 是通过递归遍历初始数据中的所有属性，并用 `Object.defineProperty` 把它们转化为 getter 和 setter 来实现数据观察的。如果一个属性在实例创建时不存在于初始数据中，那么 Vue 就没有办法观察这个属性了。

当然，你也不需要对每一个可能存在的嵌套属性都进行初始定义。在初始化的时候可以将一个属性置为空对象，然后在后面的操作中设置为一个新的拥有嵌套结构的对象。只要这个新对象包含了应有的属性，Vue 依然能对这个新对象进行递归遍历，从而观察其内部属性。

## 添加和删除属性

正如前面所说的， Vue 会使用 `Object.defineProperty` 通过转化属性值来观察数据。不过，在 ECMAScript 5 中，当一个新的属性被添加到对象或者从对象中删除的时候，并没有办法可以检测到这两种情况。为了解决这个问题，Vue 会为被观察的对象添加三个扩展方法：

- `obj.$add(key, value)`
- `obj.$set(key, value)`
- `obj.$delete(key)`

通过调用这些方法给观察对象中添加或者删除属性，就能够触发所对应的 DOM 更新。`$add` 和 `$set` 的区别是，假如当前对象已经含有所使用的 key， `$add` 会直接返回。所以当使用 `$obj.$add(key)` 的时候不会将已经存在的值覆盖为 `undefined`。

另外需要注意的一点是，当你通过数组索引赋值来改动数组时 (比如 `arr[1] = value`)，Vue 是无法侦测到这类操作的。类似地，你可以使用扩展方法来确保 Vue.js 收到了通知。被观察的数组有两个扩展方法：

- `arr.$set(index, value)`
- `arr.$remove(index | value)`

Vue 组件实例也有相应的实例方法：

- `vm.$get(path)`
- `vm.$set(path, value)`
- `vm.$add(key, value)`
- `vm.$delete(key, value)`

注意 `vm.$get` 和 `vm.set` 都接受路径。

<p class="tip">尽管存在这些方法，但我强烈建议你只在必要的时候才动态添加可观察属性。为了理解你的组件状态，将 `data` 选项看做一个 schema 很有帮助。假如你清晰地列出一个组件中所有可能存在的属性，那么当你隔了几个月再来维护这个组件的时候，就可以更容易地理解这个组件可能包含怎样的状态。</p>

## 理解异步更新

默认情况下， Vue 的 DOM 更新是**异步执行**的。理解这一点非常重要。当侦测到数据变化时， Vue 会打开一个队列，然后把在同一个事件循环 (event loop) 当中观察到数据变化的 watcher 推送进这个队列。假如一个 watcher 在一个事件循环中被触发了多次，它只会被推送到队列中一次。然后，在进入下一次的事件循环时， Vue 会清空队列并进行必要的 DOM 更新。在内部，Vue 会使用 `MutationObserver` 来实现队列的异步处理，如果不支持则会回退到 `setTimeout(fn, 0)`。

举例来说，当你设置 `vm.someData = 'new value'`，DOM 并不会马上更新，而是在异步队列被清除，也就是下一个事件循环开始时执行更新。如果你想要根据更新的 DOM 状态去做某些事情，就必须要留意这个细节。尽管 Vue.js 鼓励开发者用 “数据驱动” 的方式想问题，避免直接操作 DOM ，但有时候你可能就是想要使用某个熟悉的 jQuery 插件。这种情况下怎么办呢？你可以在数据改变后，立刻调用 `Vue.nextTick(callback)`，并把你要做的事情放到回调函数里面。当 `Vue.nextTick` 的回调函数执行时，DOM 将会已经是更新后的状态了。

示例：

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

除此之外，也有一个实例方法 `vm.$nextTick()`。这个方法和全局的 `Vue.nextTick` 功能一样，但更方便在组件内部使用，因为它不需要全局的 `Vue` 变量，另外它的回调函数的 `this` 上下文会自动绑定到调用它的 Vue 实例：

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

每一个 Vue.js 组件都是一个拥有自己的独立作用域的 Vue 实例。在使用组件的时候，理解组件作用域机制非常重要。其规则概括来说就是：

> 在父模板中出现的，将在父模板作用域内编译；在子模板中出现的，将在子模板的作用域内编译。

一个常见的错误是，在父模版中尝试将一个指令绑定到子作用域里的属性或者方法上：

``` html
<div id="demo">
  <!-- 不起作用，因为作用域不对！ -->
  <child-component v-on="click: childMethod"></child-component>
</div>
```

如果需要在子组件的根节点上绑定指令，应当将指令写在子组件的模板内：

``` js
Vue.component('child-component', {
  // 这次作用域对了
  template: '<div v-on="click: childMethod">Child</div>',
  methods: {
    childMethod: function () {
      console.log('child method invoked!')
    }
  }
})
```

注意，当组件和 `v-repeat` 一同使用时，`$index` 作为子作用域属性也会受到此规则的影响。

另外，父模板里组件节点内部的 HTML 内容被看做是 "transclusion content"（插入内容）。除非子模版包含至少一个 `<content>`出口，不然这些插入内容不会被渲染。需要留意的是，插入内容也是**在父作用域中编译**的：

``` html
<div>
  <child-component>
    <!-- 在父作用域里编译 -->
    <p>{{msg}}</p>
  </child-component>
</div>
```
你可以使用 `inline-template` 属性去明确内容在子模版的作用域中被编译：

``` html
<div>
  <child-component inline-template>
    <!-- 在子作用域里编译 -->
    <p>{{msg}}</p>
  </child-component>
</div>
```

更多关于内容插入的细节，请看组件一章的 [内容插入](/guide/components.html#内容插入) 小节。

## 在多个实例之间通讯

一种常见的在 Vue 中进行父子通讯的方法是，通过 `props` 传递一个父方法作为一个回调到子组件中。这样使用时的回调传递可以被定义在父模版中，从而保持了组件之间 Javascript 实现细节上的解耦：

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

当你需要跨越多层嵌套的组件进行通讯时，你可以使用[事件系统](/api/instance-methods.html#Events) 。另外，在构建大型应用时，用 Vue 搭配类似 [Flux](https://facebook.github.io/flux/docs/overview.html) 的架构也是完全可行的。

## 片段实例

自 0.12.2 起，replace 参数默认为 true。这意味着：

> 组件的模板长什么样，渲染出来的 DOM 就是什么样。

父模板中调用组件的元素将会被组件本身的模板取代。因此，如果组件的模板包含多个顶级元素：

``` js
Vue.component('example', {
  template:
    '<div>A</div>' +
    '<div>B</div>'
})
```

或者模板只包含文本：

``` js
Vue.component('example', {
  template: 'Hello world'
})
```

在这两个情况下，实例将变成一个**片段实例** (fragment instance)，也即没有根元素的实例。它的 $el 指向一个锚节点（普通模式下是空的文本节点，debug 模式下是注释节点）。更重要的是，父模板组件元素上的指令、过渡效果和属性绑定（props 除外）将无效，因为生成的实例并没有根元素供它们绑定：

``` html
<!-- 指令不生效，因为没有根元素用来绑定 -->
<example v-show="ok" v-transition="fade"></example>

<!-- props 还是能够正常生效 -->
<example prop="{{someData}}"></example>
```

虽然片段实例也有其使用场景，但是大部分情况下，给组件模板一个根元素是推荐的做法。这样父模板组件元素上的指令和属性能正常运转，并且性能也会更好一点。

## 修改默认选项

通过修改全局的 `Vue.options` 对象，可以修改实例选项的默认值。例如，你可以设置 `Vue.options.replace = false` ，使所有 Vue 实例都按照 `replace: false` 的规则被编译。请谨慎使用这个功能 - 最好是只在一个项目刚开始的时候使用它，因为它会影响所有 Vue 实例的行为。

下一节：[常见问题](/guide/faq.html).

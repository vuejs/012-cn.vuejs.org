title: 计算属性
type: 教程
order: 8
---

Vue.js 的内联表达式非常方便，但它最合适的使用场景是简单的布尔操作或字符串拼接。如果涉及更复杂的逻辑，你应该使用**计算属性**。

计算属性是用来声明式的描述一个值依赖了其它的值。当你在模板里把数据绑定到一个计算属性上时，Vue 会在其依赖的任何值导致该计算属性改变时更新 DOM。这个功能非常强大，它可以让你的代码更加声明式、数据驱动并且易于维护。

通常情况下使用计算属性会比使用命令式的 `$watch` 回调更何时。比如下面的例子：

``` html
<div id="demo">{{fullName}}</div>
```

``` js
var vm = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  }
})

vm.$watch('firstName', function (val) {
  this.fullName = val + ' ' + this.lastName
})

vm.$watch('lastName', function (val) {
  this.fullName = this.firstName + ' ' + val
})
```

上面的代码是声明式的并且很笨重。对比一下计算属性的版本：

``` js
var vm = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

这个更好。另外，你还可以为计算属性提供一个 setter：

``` js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

### 计算属性缓存

在 0.12.8 之前，计算属性仅仅体现为一个取值的行为 —— 每次你访问它的时候，getter 都会重新求值。在 0.12.8 中对此做了改进 —— 计算属性的值会被缓存，只有在其某个反应依赖改变才会重新计算。

设想我们有一个开销很大的计算属性 A，它需要循环一个大数组并且完成很多运算。并且我们还有一个依赖 A 的计算属性。如果没有缓存，我们对 A 的 getter 不必要的过度调用就会导致潜在的性能问题。而有了缓存，A 的值会被缓存起来，除非其依赖发生了变化，这样访问它再多次也不会导致大量的不必要运算了。

然而，我们还是应该理解什么会被视为“反应式依赖”：

``` js
var vm = new Vue({
  data: {
    msg: 'hi'
  },
  computed: {
    example: {
      return Date.now() + this.msg
    }
  }
})
```

在上面的例子中，计算属性依赖 `vm.msg`。因为这是一个在 Vue 实例中被观察的数据属性，那么它就被视为了一个反应式依赖。无论何时 `vm.msg` 改变，`vm.example` 的值都会被重新计算。

然而，`Date.now()` **并不是**反应式依赖，因为它没有和 Vue 的数据观察系统发生任何关系。因此，当你在程序中访问 `vm.example` 时，你会发现除非 `vm.msg` 触发了一次重新计算，否则时间戳始终是相同的值。

有的时候你需要保留简单获取数据的模式，每次你访问 `vm.example` 的时候都希望触发重新计算。从 0.12.11 开始，你可以为一个特殊的计算属性开关缓存支持：

``` js
computed: {
  example: {
    cache: false,
    get: function () {
      return Date.now() + this.msg
    }
  }
}
```

现在，每次你访问 `vm.example` 的时候，时间戳都会及时更新。然而，要注意这只发生在 JavaScript 程序内部访问的时候，数据绑定还是依赖驱动的。当你在模板中绑定一个 `{% raw %}{{example}}{% endraw %}` 的计算属性时，DOM 只会在反应式依赖改变时才会更新。

接下来，让我们学习一下如何[编写自定义指令](../guide/custom-directive.html)。

title: 计算属性
type: 教程
order: 8
---

Vue.js 的内联表达式非常方便，但它最合适的使用场景是简单的布尔操作或字符串拼接。如果涉及更复杂的逻辑，你应该使用**计算属性**。

A computed property is used to declaratively describe a value that depends on other values. When you data-bind to a computed property inside the template, Vue knows when to update the DOM when any of the values depended upon by the computed property has changed. This can be very powerful and makes your code more declarative, data-driven and thus easier to maintain.

It is often a better idea to use a computed property rather than an imperative `$watch` callback. Consider this example:

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

The above code is imperative and cumbersome. Compare it with a computed property version:

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

Much better. In addition, you can also provide a setter for a computed property:

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

### Computed Poperty Caching

在 0.12.8 之前，计算属性仅仅体现为一个取值的行为 —— 每次你访问它的时候，getter 都会重新求值。在 0.12.8 中对此做了改进 —— 计算属性的值会被缓存，只有在其某个反应依赖改变才会重新计算。

Imagine we have an expensive computed property A, which requires looping through a huge Array and doing a lot of computations. Then, we may have other computed properties that depend on A. Without caching, we'd be calling A's getter many more times than necessary and this could potentially cause performance issues. With caching, A's value will be cached as long as its dependencies haven't changed, and accessing it many times will not trigger unnecessary computations.

However, it is important to understand what is considered a "reactive dependency":

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

In the example above, the computed property relies on `vm.msg`. Because this is an observed data property on the Vue instance, it is considered a reactive dependency. Whenever `vm.msg` is changed, `vm.example`'s value will be re-evaludated.

However, `Date.now()` is **not** a reactive dependency, because it has nothing to do with Vue's data observation system. Therefore, when you programatically access `vm.computed`, you will find the timestamp to remain the same unless `vm.msg` triggered a re-evaluation.

Sometimes you may want to preserve the simple getter-like behavior, where every time you access `vm.example` it is simply re-evaluated. Starting in 0.12.11, it's possible to turn off caching for a specific computed property:

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

Now, every time you access `vm.example`, the timestamp will be up-to-date. However, note this only affects programmatic access inside JavaScript; data-bindings are still dependency-driven. When you bind to a computed property in the template as `{% raw %}{{example}}{% endraw %}`, the DOM will only be updated when a reactive dependency has changed.

接下来，让我们学习一下如何[编写自定义指令](../guide/custom-directive.html)。

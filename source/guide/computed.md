title: 计算属性
type: 教程
order: 8
---

Vue.js 的内联表达式非常方便，但它最合适的使用场景是简单的布尔操作或字符串拼接。如果涉及更复杂的逻辑，你应该使用**计算属性**。

在 Vue.js 中，你可以通过 `computed` 选项定义计算属性：

``` js
var demo = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: {
      // getter 应返回计算后的值
      get: function () {
        return this.firstName + ' ' + this.lastName
      },
      // setter 是可选的
      set: function (newValue) {
        var names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
  }
})

demo.fullName // 'Foo Bar'
```

当你只需要 getter 的时候，你可以直接提供一个函数：

``` js
// ...
computed: {
  fullName: function () {
    return this.firstName + ' ' + this.lastName 
  }    
}
// ...
```

一个计算属性本质上是一个被 getter/setter 函数定义了的属性。计算属性使用起来和一般属性一样，只是在访问它的时候，你会得到 getter 函数返回的值，改变它的时候，你会触发 setter 函数，新值将会作为 setter 的参数被传入。

在 0.12.8 之前，计算属性仅仅体现为一个取值的行为 —— 每次你访问它的时候，getter 都会重新求值。在 0.12.8 中对此做了改进 —— 计算属性的值会被缓存，只有在需要时才会重新计算。

接下来，让我们学习一下如何[编写自定义指令](../guide/custom-directive.html)。

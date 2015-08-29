title: 事件监听
type: 教程
order: 6
---

你可以使用 `v-on` 指令来绑定并监听 DOM 事件。绑定的内容可以是一个当前实例上的方法 (后面无需跟括号) 或一个内联表达式。如果提供的是一个方法，则原生的 DOM event 会被作为第一个参数传入，同时这个 event 会带有 `targetVM` 属性，指向触发该事件的相应的 ViewModel：

``` html
<div id="demo">
  <a v-on="click: onClick">触发一个方法函数</a>
  <a v-on="click: n++">触发一个表达式</a>
</div>
```

``` js
new Vue({
  el: '#demo',
  data: {
    n: 0
  },
  methods: {
    onClick: function (e) {
      console.log(e.target.tagName) // "A"
      console.log(e.targetVM === this) // true
    }
  }
})
```

## 执行表达式

当在 `v-repeat` 里使用 `v-on` 时，`targetVM` 显得很有用，因为 `v-repeat` 会创建大量子 ViewModel。但是，通过执行表达式的方式，把代表当前 ViewModel 数据对象的别名传进去，会更方便直观一些：

``` html
<ul id="list">
  <li v-repeat="item in items" v-on="click: toggle(item)">
    {{text}}
  </li>
</ul>
```

``` js
new Vue({
  el: '#list',
  data: {
    items: [
      { text: 'one', done: true },
      { text: 'two', done: false }
    ]
  },
  methods: {
    toggle: function (item) {
      item.done = !item.done
    }
  }
})
```

当你想要在表达式中访问原来的 DOM event，你可以传递一个 `$event` 参数进去：

``` html
<button v-on="click: submit('hello!', $event)">Submit</button>
```

``` js
/* ... */
{
  methods: {
    submit: function (msg, e) {
      e.stopPropagation()
    }
  }
}
/* ... */
```

## `key` 过滤器

当监听键盘事件时，我们常常需要判断常用的 key code。Vue.js 提供了一个特殊的只能用在 `v-on` 指令的过滤器：`key`。它接收一个表示 key code 的参数并完成判断：

``` html
<!-- 只有当 keyCode 等于 13 时才调用方法 -->
<input v-on="keyup:submit | key 13">
```

它也预置了一些常用的按键名：

``` html
<!-- 效果同上 -->
<input v-on="keyup:submit | key 'enter'">
```

查看API参考：[key 过滤器的全部预设参数](../api/filters.html#key).

## 为什么要在 HTML 中写监听器？

你可能会注意到整个事件监听的方式违背了 “separation of concern” 的传统理念。不必担心，因为所有的 Vue.js 事件处理方法和表达式都严格绑定在当前视图的 ViewModel 上，它不会导致任何维护困难。实际上，使用 `v-on` 还有更多好处：

1. 它便于在 HTML 模板中轻松定位 JS 代码里的对应方法实现。
2. 因为你无须在 JS 里手动绑定事件，你的 ViewModel 代码可以是非常纯粹的逻辑，和 DOM 完全解耦。这会更易于测试。
3. 当一个 ViewModel 被销毁时，所有的事件监听都会被自动移除。你无须担心如何自行清理它们。

下一步：[处理表单](../guide/forms.html)。

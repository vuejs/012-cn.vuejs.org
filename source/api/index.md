title: "Vue 构造函数"
type: api
order: 1
---

`Vue` 的构造函数是 Vue.js 的核心。它允许你创建 Vue 实例。创建一个 Vue 实例非常简单明了：

``` js
var vm = new Vue({ /* options */ })
```

当你初始化一个 Vue 实例，你需要传递一个包括 DOM 对象，data 对象，mixin 方法，生命周期回调函数等内容的option对象。完整列表见[组件参数](../api/options.html)。

每个Vue实例本质上就是一个 ViewModel (因此在本文档中你会看到好多变量名叫`vm`)。每个实例都有一个 DOM 对象属性叫`$el`，它大致相当于 MVVM 中的 V。每个实例也有一个 JavaScript 对象属性叫 `$data`，相对应的就是 MVVM 中的 M。改变 M 会触发 V 的更新。对于双向绑定，用户对 V 的介入会触发 M 中的变化。Vue 实例中详细的可用属性请看[实例属性](../api/instance-properties.html)。

每个 Vue 实例也有不少[实例方法](../api/instance-methods.html)，包括数据监视，事件通讯和 DOM 操作。

每个 `Vue` 构造函数本身也会暴露[全局 API](../api/global-api.html)，这些 API 可以让你扩展 `Vue` 的 class，配置全局选项，注册全局组件、指令、过滤器等更多自定义资源。

## 初始化

如果你在实例化中提供了 `el` 选项，Vue 实例将会立即进入编译阶段。否则，编译工作直到其 `vm.$mount()` 被调用时才会开始。在编译阶段，Vue 会走入 DOM 并收集其中的指令，将其数据和这些指令所在的 DOM “链接”起来。一旦链接完毕，这些 DOM 结点就会被 Vue 实例正式接管。一个 DOM 结点只能被一个 Vue 实例接管，并且不能被多次编译。

## 数据代理

Vue 实例通过代理方法访问他们的 `$data` 对象，如果你有个变量叫 msg，你可以通过 `vm.$data.msg` 访问也可以通过 `vm.msg` 来访问。看起来有点神奇，但这完全是可选的。你完全可以就用 `vm.$data.msg` 这种方式来访问。但不管怎样，我们仍需要注意到 `vm` 和 `vm.$data` 的区别，因为前者是不能作为数据对象被别的 Vue 实例监视的。

还有一点值得注意的是数据对象 (data objects) 并不只属于一个 Vue 实例，多个 ViewModel 可以监视同一个数据对象，不论是 `$data` 本身还是它的属性。当多个组件需要共享一个全局状态数据对象的时候，这个特性就非常有用。

title: 实例方法
type: api
order: 4
---

## 数据

> 你可以在 Vue 实例中监视数据的变化。需要注意的是，所有的 watch 回调都是异步发生的，而且这些回调和指令一样被进行了异步批量处理。这就意味着如果一份数据在同一个事件循环 (event loop) 中多次变化，只会以最后一个值触发一次回调。

### vm.$watch( expOrFn, callback, [options] )

- **expOrFn** `String|Function`
- **callback( newValue, oldValue )** `Function`
- **options** `Object` *optional*
  - **deep** `Boolean`
  - **immediate** `Boolean`
  - **sync** `Boolean`

在当前 Vue 实例上监听一个表达式或是一个计算函数的值。

``` js
vm.$watch('a + b', function (newVal, oldVal) {
  // do something
})
// or
vm.$watch(
  function () {
    return this.a + this.b
  },
  function (newVal, oldVal) {
    // do something
  }
)
```

如果要监视在对象中深层嵌套的属性变化，需要在 `options` 参数里传递 `deep: true` 来启用深度数据观察。注意在监听数组本身结构变化 (mutations) 的时候不需要此参数。

``` js
vm.$watch('someObject', callback, {
  deep: true
})
vm.someObject.nestedValue = 123
// 回调被触发
```

如果在 `options` 参数里加上 `immediate: true`，那么回调会带着现在表达式的结果被立即触发。

``` js
vm.$watch('a', callback, {
  immediate: true
})
// 回调使用现在表达式的结果 `a` 被立刻触发
```

最后，`vm.$watch` 会返回一个停止数据观察的句柄函数。

``` js
var unwatch = vm.$watch('a', cb)
// 停止观察
unwatch()
```

### vm.$get( expression )

- **expression** `String`

为 Vue 实例传递一个表达式来获得结果，如果表达式抛错，该错误会被截获并返回 `undefined`。

### vm.$set( keypath, value )

- **keypath** `String`
- **value** `*`

通过给 Vue 实例传递一个可用的路径来设置结果，如果路径不存在那么会被递归创建。 

### vm.$add( key, value )

- **key** `String`
- **value** `*`

为 Vue 实例及其 `$data` 对象添加一个顶层属性 (root level property)。由于 ES5 的限制，Vue 无法侦测到对象中属性的增加或者删除，所以当你需要动态添加/删除属性的时候请使用此方法和 `vm.$delete`，但请谨慎使用，因为此方法会使得当前 vm 对所有 watcher 进行一次脏检查。

### vm.$delete( key )

- **key** `String`

在 Vue 实例及其 `$data` 对象中删除一个顶层属性。

### vm.$eval( expression )

- **expression** `String`

对一个表达式求值，该表达式可以包含过滤器。

``` js
// 假定 vm.msg = 'hello'
vm.$eval('msg | uppercase') // -> 'HELLO'
```

### vm.$interpolate( templateString )

- **templateString** `String`

对一段包含 Mustache 插值的模板字符串进行处理。注意：此方法仅仅处理字符串插值，属性指令不会被编译。


``` js
// 假定 vm.msg = 'hello'
vm.$interpolate('{{msg}} world!') // -> 'hello world!'
```

### vm.$log( [keypath] )

- **keypath** `String` *optional*

以一个纯对象的方式打印当前实例的数据，比直接打印数据本身更容易在控制台里观察。接受一个可选路径参数。

``` js
vm.$log() // 打印整个 ViewModel 数据
vm.$log('item') // 只打印 vm.item
```

## 事件系统

> 每个 vm 都是一个事件触发器。当你有多层嵌套的组件时，你可以用事件系统去在它们之间进行通信。

### vm.$dispatch( event, [args...] )

- **event** `String`
- **args...** *optional*

从当前的 vm 向上传递一个事件，一直到它的 `$root` 实例为止。如果传递过程中任意一个回调返回了 `false` ，那么事件会在该回调所属的实例处停止传播。

### vm.$broadcast( event, [args...] )

- **event** `String`
- **args...** *optional*

向当前 vm 的所有子 vm 向下广播该事件。广播会进行深度遍历。如果传递过程中一个回调返回了 `false`，那么该回调的所属实例就不会继续向下广播这个事件。

### vm.$emit( event, [args...] )

- **event** `String`
- **args...** *optional*

只在当前 vm 上触发一个事件。

### vm.$on( event, callback )

- **event** `String`
- **callback** `Function`

在当前 vm 上监听一个事件。

### vm.$once( event, callback )

- **event** `String`
- **callback** `Function`

在当前 vm 上为此事件绑定一个一次性的监听器。

### vm.$off( [event, callback] )

- **event** `String` *optional*
- **callback** `Function` *optional*

如果没有传递参数，那么停止监视所有事件；如果传递了一个事件，那么移除该事件的所有回调；如果事件和回调都被传递，则只移除该回调。

## DOM

> vm 的 DOM 操作方法和 jQuery 中的同名方法类似，不同点在于 vm 实例的 DOM 操作方法能够触发 Vue.js 的过渡效果。 关于过渡效果，请参考 [过渡效果](../guide/transitions.html)。

### vm.$appendTo( element|selector, [callback] )

- **element** `HTMLElement` | **selector** `String`
- **callback** `Function` *optional*

将 vm 的 `$el` 插入到目标元素的最后。参数可以是一个元素，或者是一个 querySelector 的选择器。

### vm.$before( element|selector, [callback] )

- **element** `HTMLElement` | **selector** `String`
- **callback** `Function` *optional*

在目标元素之前插入 vm 的 `$el` 。

### vm.$after( element|selector, [callback] )

- **element** `HTMLElement` | **selector** `String`
- **callback** `Function` *optional*

在目标元素之后插入 vm 的 `$el` 。

### vm.$remove( [callback] )

- **callback** `Function` *optional*

从 DOM 中移除 vm 的 `$el` 。

### vm.$nextTick( callback )

- **callback** `Function`

延迟回调的执行直到下一次 DOM 更新循环结束。当你改变一些数据之后调用它，当回调触发时 DOM 已经更新完毕。这和全局的 `Vue.nextTick` 效果相同，只是回调的 `this` 上下文会自动指向调用此方法的实例。

## 生命周期

### vm.$mount( [element|selector] )

- **element** `HTMLElement` | **selector** `String` *optional*

如果 Vue 实例在实例化的时候没有被传递一个 `el` 的选项,你可以自己调用 `$mount()` 来启动编译阶段。默认情况下，被挂载的元素将被实例的模板替代。假如 `replace` 选项被设置成 `false`，则模板将会被插入到挂载的元素中并且覆盖其内部的原始内容。除非模板包含 `<content>` 出口，否则原始内容会被丢弃。

如果没有提供参数，模板将会被生成为一个文档之外的元素，并且你需要亲自使用 DOM 实例方法把它插入到文档里。如果 `replace` 选项被设置成 `false` ，那么就会自动生成一个空的 `<div>` 作为封装元素。在已经被挂载的实例上调用 `$mount()` 上是没有作用的。这个方法会返回实例本身，所以如果你可以配合其他实例方法进行链式调用。

### vm.$destroy( [remove] )

- **remove** `Boolean` *optional* (默认值: `false`)

完全销毁一个 vm。清除它与其他 vm 的关联，解除所有指令的绑定。并可选地从DOM中移除它的 `$el`。同时，所有的 `$on` 和 `$watch` 监听器也都会被自动移除。

### vm.$compile( element )

- **element** `HTMLElement`

局部编译一块 DOM (元素或者是文档片段)。此方法返回一个 `decompile` 函数，用来清理编译过程中生成的指令。注意 decompile 函数不会移除被编译的 DOM。这个方法主要是为高级插件的编写者提供的。

### vm.$addChild( [options, constructor] )

- **options** `Object` *optional*
- **constructor** `Function` *optional*

为当前的实例增加一个子实例。选项对象和手动实例化时所用的选项是相同的。额外还可以传递一个用 `Vue.extend()` 创造的构造函数。

实例之间的父子关系有三个含义：

1. 父实例和子实例可以通过[事件系统](#事件系统)来通信。
2. 子实例可以访问父实例的所有资源（比如：自定义指令）。但在严格模式下这一条会被禁止。
3. 如果是继承了父实例作用域的子实例，也可以访问父实例的数据属性。

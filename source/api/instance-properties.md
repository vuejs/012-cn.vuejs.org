title: 实例属性
type: api
order: 3
---

### vm.$el

- **Type:** `HTMLElement`
- **只读**

返回当前 Vue 实例正在管理的 DOM 元素。注意对于[片段实例](/guide/best-practices.html#片段实例)，`vm.$el` 指向的是一个代表片段起始位置的锚节点。

### vm.$data

- **Type:** `Object`

返回当前 Vue 实例正在监视的数据对象 (data object)。你可以用新的对象去替换它。Vue 实例会代理其 $data 对象上的所有属性。

### vm.$options

- **Type:** `Object`

返回当前 Vue 实例所使用的实例化选项。如果你想要调用自定义选项，就会需要用到这个属性:

``` js
new Vue({
  customOption: 'foo',
  created: function () {
    console.log(this.$options.customOption) // -> 'foo'
  }
})
```

### vm.$parent

- **Type:** `Vue`
- **只读**

返回当前 vm 的父实例（如果存在的话）。

### vm.$root

- **Type:** `Vue`
- **只读**

返回当前组件树的根 Vue 实例。如果当前实例已经没有父实例的话将会返回它自己。

### vm.$children

- **Type:** `Array<Vue>`
- **只读**

返回当前实例的直接子实例数组。

### vm.$

- **Type:** `Object`
- **只读**

返回一个对象，这个对象包含通过 `v-ref` 指令注册的子组件。更多细节请查看 [v-ref](../api/directives.html#v-ref)。

### vm.$$

- **Type:** `Object`
- **Read only**

返回一个对象，这个对象包含通过 `v-el` 指令注册的 DOM 元素。更多细节请查看 [v-el](../api/directives.html#v-el)。

### 元属性

被 `v-repeat` 创建出来的实例也会拥有一些元属性 (Meta properties)。例如 `vm.$index`, `vm.$key` 和 `vm.$value` 等。更多细节请查看教程中的 [列表渲染](../guide/list.html) 章节。

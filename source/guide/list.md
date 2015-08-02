title: 渲染列表
type: 教程
order: 5
---

你可以使用 `v-repeat` 指令来基于 ViewModel 上的对象数组渲染列表。对于数组中的每个对象，该指令将创建一个以该对象作为其 `$data` 对象的子 Vue 实例。这些子实例继承父实例的数据作用域，因此在重复的模板元素中你既可以访问子实例的属性，也可以访问父实例的属性。此外,你还可以通过 `$index` 属性来获取当前实例对应的数组索引。

**示例：**

``` html
<ul id="demo">
  <li v-repeat="items" class="item-{{$index}}">
    {{$index}} - {{parentMsg}} {{childMsg}}
  </li>
</ul>
```

``` js
var demo = new Vue({
  el: '#demo',
  data: {
    parentMsg: 'Hello',
    items: [
      { childMsg: 'Foo' },
      { childMsg: 'Bar' }
    ]
  }
})
```

**结果：**

<ul id="demo"><li v-repeat="items" class="item-{&#123;$index&#125;}">{&#123;$index&#125;} - {&#123;parentMsg&#125;} {&#123;childMsg&#125;}</li></ul>
<script>
var demo = new Vue({
  el: '#demo',
  data: {
    parentMsg: 'Hello',
    items: [
      { childMsg: 'Foo' },
      { childMsg: 'Bar' }
    ]
  }
})
</script>

## 块级重复

有时我们可能需要重复一个包含多个节点的块，这时可以用 `<template>` 标签包裹这个块。这里的 `<template>` 标签只起到语义上的包裹作用，其本身不会被渲染出来。例如：

``` html
<ul>
  <template v-repeat="list">
    <li>{{msg}}</li>
    <li class="divider"></li>
  </template>
</ul>
```

## 简单值数组

简单值 (primitive value) 即字符串、数字、boolean 等并非对象的值。对于包含简单值的数组，你可用 `$value` 直接访问值:

``` html
<ul id="tags">
  <li v-repeat="tags">
    {{$value}}
  </li>
</ul>
```

``` js
new Vue({
  el: '#tags',
  data: {
    tags: ['JavaScript', 'MVVM', 'Vue.js']
  }
})
```

**结果：**
<ul id="tags" class="demo"><li v-repeat="tags">{&#123;$value&#125;}</li></ul>
<script>
new Vue({
  el: '#tags',
  data: {
    tags: ['JavaScript', 'MVVM', 'Vue.js']
  }
})
</script>

## 使用别名

有时我们可能想要更明确地访问当前作用域的变量而不是隐式地回退到父作用域。你可以通过提供一个参数给 `v-repeat` 指令并用它作为将被迭代项的别名:

``` html
<ul id="users">
  <li v-repeat="user in users">
    {{user.name}} - {{user.email}}
  </li>
</ul>
```

``` js
new Vue({
  el: '#users',
  data: {
    users: [
      { name: 'Foo Bar', email: 'foo@bar.com' },
      { name: 'John Doh', email: 'john@doh.com' }
    ]
  }
})
```

**结果：**
<ul id="users" class="demo"><li v-repeat="user in users">{&#123;user.name&#125;} - {&#123;user.email&#125;}</li></ul>
<script>
new Vue({
  el: '#users',
  data: {
    users: [
      { name: 'Foo Bar', email: 'foo@bar.com' },
      { name: 'John Doh', email: 'john@doh.com' }
    ]
  }
})
</script>

<p class="tip">这里的 `user in users` 语法只在 Vue 0.12.8 及其以上版本中可用。老版本需要使用 `user : users` 语法。</p>

<p class="tip">在 `v-repeat` 中使用别名会让模板可读性更强，同时性能更好。</p>

## 变异方法

Vue.js 内部对被观察数组的变异方法 (mutating methods，包括 `push()`, `pop()`, `shift()`, `unshift()`, `splice()`, `sort()` 和 `reverse()`) 进行了拦截，因此调用这些方法也将自动触发视图更新。

``` js
// 以下操作会触发 DOM 更新
demo.items.unshift({ childMsg: 'Baz' })
demo.items.pop()
```

## 扩展方法

Vue.js 给被观察数组添加了两个便捷方法：`$set()` 和 `$remove()` 。

你应该避免直接通过索引来设置数据绑定数组中的元素，比如 `demo.items[0] = {}`，因为这些改动是无法被 Vue.js 侦测到的。你应该使用扩展的 `$set()` 方法:

``` js
// 不要用 `demo.items[0] = ...`
demo.items.$set(0, { childMsg: 'Changed!'})
```

`$remove()` 只是 `splice()`方法的语法糖。它将移除给定索引处的元素。当参数不是数值时，`$remove()` 将在数组中搜索该值并删除第一个发现的对应元素。

``` js
// 删除索引为 0 的元素。
demo.items.$remove(0)
```

## 替换数组

当你使用非变异方法，比如`filter()`, `concat()` 或 `slice()`，返回的数组将是一个不同的实例。在此情况下，你可以用新数组替换旧的数组:

``` js
demo.items = demo.items.filter(function (item) {
  return item.childMsg.match(/Hello/)
})
```

你可能会认为这将导致整个列表的 DOM 被销毁并重新渲染。但别担心，Vue.js 能够识别一个数组元素是否已有关联的 Vue 实例， 并尽可能地进行有效复用。

## 使用 `track-by`

在某些情况下，你可能需要以全新的对象（比如 API 调用所返回的对象）去替换数组。如果你的每一个数据对象都有一个唯一的 id 属性，那么你可以使用 `track-by` 特性参数给 Vue.js 一个提示，这样它就可以复用已有的具有相同 id 的 Vue 实例和 DOM 节点。

例如，如果你的数据长这样:

``` js
{
  items: [
    { _uid: '88f869d', ... },
    { _uid: '7496c10', ... }
  ]
}
```

那么你可以像这样给出提示:

``` html
<div v-repeat="items" track-by="_uid">
  <!-- content -->
</div>
```

在替换 `items` 数组时，Vue.js 如果碰到一个有 `_uid: '88f869d'` 的新对象，它就会知道可以直接复用有同样 _uid 的已有实例。

如果没有唯一的 id 可以用来追踪，也可以使用 `track-by="$index"`，也就是原地复用实例和 DOM 节点。请务必小心使用此功能，因为在追踪 `$index` 时，Vue 不会移动子实例及 DOM 节点，只是按他们第一次被创建时的顺序复用。有两种情形应避免使用 `track-by="$index"`：

1. 当重复的块包含表单输入框，且输入框绑定的值可能导致数组被重新排序；
2. 在重复组件时，组件除了接收的数组数据外，还包含私有的状态。

<p class="tip">在使用全新数据重新渲染大型 `v-repeat` 列表时，合理使用 `track-by` 能极大地提升性能。</p>

## 遍历对象

你也可以使用 `v-repeat` 遍历一个对象的所有属性。每个重复的实例会有一个特殊的属性 `$key`。对于简单值，你也可以象访问数组中的简单值那样使用 `$value` 属性。

``` html
<ul id="repeat-object">
  <li v-repeat="primitiveValues">{{$key}} : {{$value}}</li>
  <li>===</li>
  <li v-repeat="objectValues">{{$key}} : {{msg}}</li>
</ul>
```

``` js
new Vue({
  el: '#repeat-object',
  data: {
    primitiveValues: {
      FirstName: 'John',
      LastName: 'Doe',
      Age: 30
    },
    objectValues: {
      one: {
        msg: 'Hello'
      },
      two: {
        msg: 'Bye'
      }
    }
  }
})
```

**结果:**
<ul id="repeat-object" class="demo"><li v-repeat="primitiveValues">{&#123;$key&#125;} : {&#123;$value&#125;}</li><li>===</li><li v-repeat="objectValues">{&#123;$key&#125;} : {&#123;msg&#125;}</li></ul>
<script>
new Vue({
  el: '#repeat-object',
  data: {
    primitiveValues: {
      FirstName: 'John',
      LastName: 'Doe',
      Age: 30
    },
    objectValues: {
      one: {
        msg: 'Hello'
      },
      two: {
        msg: 'Bye'
      }
    }
  }
})
</script>

<p class="tip">在 ECMAScript 5 中，对于给对象添加一个新属性，或是从对象中删除一个属性时，没有机制可以检测到这两种情况。针对这个问题，Vue.js 中的被观察对象会被添加三个扩展方法: `$add(key, value)`, `$set(key, value)` 和 `$delete(key)`。这些方法可以被用于在添加 / 删除观察对象的属性时触发对应的视图更新。方法 `$add` 和 `$set` 的不同之处在于当指定的键已经在对象中存在时 `$add` 将提前返回，所以调用 `obj.$add(key)` 并不会以 `undefined` 覆盖已有的值。</p>

## 迭代值域

`v-repeat` 也可以接受一个整数。在这种情况下，它将重复显示一个模板多次.

``` html
<div id="range">
    <div v-repeat="val">Hi! {{$index}}</div>
</div>
```

``` js
new Vue({
  el: '#range',
  data: {
    val: 3
  }
});
```
**结果:**
<ul id="range" class="demo"><li v-repeat="val">Hi! {&#123;$index&#125;}</li></ul>
<script>
new Vue({
  el: '#range',
  data: { val: 3 }
});
</script>

## 数组过滤器

有时候我们只需要显示一个数组的过滤或排序过的版本，而不需要实际改变或重置原始数据。Vue 提供了两个内置的过滤器来简化此类需求： `filterBy` 和 `orderBy`。可查看 [方法文档](../api/filters.html#filterBy) 获得更多细节。

下一步: [事件监听](../guide/events.html)。

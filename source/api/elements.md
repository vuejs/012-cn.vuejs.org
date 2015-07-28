title: 特殊元素
type: api
order: 8
is_new: true
---

> 下面是在 Vue.js 模板中一些出于特殊目的设计的抽象元素的列表

### component

使用组件的另一种语法。主要配合 `is` 特性用于动态化的组件：

``` html
<!-- a dynamic component controlled by -->
<!-- the `componentId` property on the vm -->
<component is="{{componentId}}"></component>
```

### content

`<content>` 标签用于作为组件模板的内容插入点，而 `<content>` 元素本身会被真正的内容所替换。它接受一个 `select` 的可选特性，该特性应该是一个合法的 CSS 选择器，这样只有匹配这一选择器的被插入内容的子集才会被展示：

``` html
<!-- only display <li>'s in the inserted content -->
<content select="li"></content>
```

其实 select 特性还可以包含 mustache 表达式。更多细节参见[内容插入](/guide/components.html#Content_Insertion)。

### partial

`<partial>` 标签用于作为注册片段的结点。片段内容在插入时也会被 Vue 编译，同时 `<partial>` 元素本身会被替换掉。它需要提供一个 `name` 特性。比如：

``` js
// registering a partial
Vue.partial('my-partial', '<p>This is a partial! {{msg}}</p>')
```

``` html
<!-- a static partial -->
<partial name="my-partial"></partial>

<!-- a dynamic partial -->
<partial name="{{partialId}}"></partial>
```

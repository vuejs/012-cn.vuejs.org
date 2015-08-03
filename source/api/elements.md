title: 特殊元素
type: api
order: 8
is_new: true
---

> 下面是一些 Vue.js 内置的抽象元素，它们在模板中有特殊作用。

### component

使用组件的另一种语法。主要配合 `is` 特性用于动态化的组件：

``` html
<!-- 动态组件，由 vm 的 `componentId` 属性控制 -->
<component is="{{componentId}}"></component>
```

### content

`<content>` 标签用于作为组件模板的内容插入点，而 `<content>` 元素本身会被真正的内容所替换。它接受一个 `select` 的可选特性，该特性应该是一个合法的 CSS 选择器，这样只有匹配这一选择器的被插入内容的子集才会被展示：

``` html
<!-- 只显示插入内容中的 <li> -->
<content select="li"></content>
```

其实 select 特性还可以包含 mustache 表达式。更多细节参见[内容插入](/guide/components.html#内容插入)。

### partial

`<partial>` 用作注册的模板片段的插入点。片段内容在插入时也会被 Vue 编译，同时 `<partial>` 元素本身会被替换掉。它需要提供一个 `name` 特性。比如：

``` js
// 注册模板片段
Vue.partial('my-partial', '<p>This is a partial! {{msg}}</p>')
```

``` html
<!-- 静态模板片段 -->
<partial name="my-partial"></partial>

<!-- 动态模板片段 -->
<partial name="{{partialId}}"></partial>
```

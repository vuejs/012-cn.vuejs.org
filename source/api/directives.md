title: 指令
type: api
order: 6
---

## 响应式指令 

> 指令可以将自己与一个 Vue 实例上的属性绑定，也可以与一个实例作用域中的表达式绑定。当属性或表达式的值发生改变时，指令的 `update()` 方法会在下一个事件循环中被异步调用。

### v-text

更新元素的 `textContent`。

{&#123; Mustache &#125;} 的插值也会被当做文字节点上的 `v-text` 指令进行编译。

### v-html

更新元素的 `innerHTML`。

将 `v-html` 绑定到用户提供的数据上会有 XSS 的风险，因此使用 `v-html` 时应确保数据的安全性，或通过自定义过滤器将不被信任的 HTML 内容进行预处理。

### v-show

- 可触发过渡效果。

根据绑定值将元素的 display CSS 属性设置为 `none` 或它的原始值。

### v-class

- 接受一个可选的参数

如果没有提供参数，则将绑定值作为 CSS 类命字符串添加到元素的 classList 中。
 
如果提供了参数，则会以参数为 CSS 类名，并根据绑定值的真伪进行切换。可以配合多重从句使用：

``` html
<span v-class="
  red    : hasError,
  bold   : isImportant,
  hidden : isHidden
"></span>
```

或者，你可以直接将该指令绑定到一个对象。对象的键值会作为类名被添加到元素的 classList，并根据对应值的真伪切换。

### v-attr

- 需要一个参数

更新元素的指定特性（由参数指定）

**示例:**

``` html
<canvas v-attr="width:w, height:h"></canvas>
```

除 0 外的其他伪值将移除该特性。

或者，你可以直接将该指令绑定到一个对象。对象的键会被作为特性名称，根据对应的值的真伪进行切换。

在内部，普通特性中的 {&#123; Mustache &#125;} 插值会被转换为 `v-attr` 指令进行编译。

从 0.12.9 开始，当 `v-attr` 直接用在 `<input>` 元素的 `value` 特性中时，实际设置的会是其 `value` 属性 (property) 而不是特性 (attribute)。例如，`<input value="{% raw %}{{val}}{% endraw %}">` 不会更新其特性值，而是会直接设置其属性值。
 
<p class="tip">在为 `<img>` 元素设置 `src` 特性时，应该使用 `v-attr` 绑定而不是 mustache 模板绑定。浏览器会先于 Vue.js 对你的模板进行解析。所以当浏览器试图获取图片 URL 时，使用 mustache 模板绑定的数据会导致 404 错误。</p>

### v-style

- 接受一个可选参数

为元素添加内联 CSS 样式。

如果没有参数名，绑定值可以是一个字符串或是一个对象。

- 如果绑定值为字符串，则会将该元素的 `style.cssText` 属性设置为参数的值。
- 如果绑定值为对象，对象里的每一对键值都将被赋值到元素的 style 对象上。

**示例:**

``` html
<div v-style="myStyles"></div>
```

``` js
// myStyles 可以是字符串:
"color:red; font-weight:bold;"
// 也可以是对象:
{
  color: 'red',
  // CSS 规则用驼峰格式和连字符格式都可以
  fontWeight: 'bold',
  'font-size': '2em'
}
```

如果有参数的话，参数会被当作 CSS 规则名来用。可以配合多重从句添加多条规则：

**示例:**

``` html
<div v-style="
  top: top + 'px',
  left: left + 'px',
  background-color: 'rgb(0,0,' + bg + ')'
"></div>
```

`v-style` 会智能地检测 CSS 规则是否需要浏览器前缀，所以你可以放心的使用无前缀版本的 CSS 规则。

``` html
<!-- 如果需要的话会自动使用 -webkit-transform -->
<div v-style="transform: 'scale(' + scale + ')'"></div>
```

<p class="tip">由于 IE 的所有版本都会在解析 HTML 时将无法解析的内联样式删除，因此推荐统一使用 `v-style` 指令来代替 style 特性中的 &#123;&#123;mustache&#125;&#125; 插值。</p>

### v-on

- 需要一个参数。
- 绑定值应该是一个函数或者声明。

为元素添加一个 DOM 事件监听器。事件的类型由参数指定。这也是唯一可以和 `key` 过滤器一起使用的指令。详细请见[事件监听](../guide/events.html)。

### v-model

- 只能用于 `<input>`, `<select>` 或 `<textarea>` 元素。
- 指令特性参数: [`lazy`](/guide/forms.html#惰性更新), [`number`](/guide/forms.html#转换为数字), [`options`](/guide/forms.html#动态_select_选项), [`debounce`](/guide/forms.html#输入_Debounce)

在表单 input 元素上创建一个双向绑定。默认在 input 事件时同步数据。详情请见[处理表单](../guide/forms.html).

### v-if

- 可触发过渡效果。

基于绑定值的真伪，插入或移除元素。如果元素为 `<template>` 元素，则它的内容将会被提取出来作为被插入或移除的片段。

**示例:**

``` html
<template v-if="test">
  <p>hello</p>
  <p>world</p>
</template>
```

渲染结果:

``` html
<!--v-if-start-->
<p>hello</p>
<p>world</p>
<!--v-if-end-->
```

### v-repeat

- 会创建子 Vue 实例。
- 绑定值应为一个数组、对象或数字。
- 可触发过渡效果。
- 接受一个可选参数。
- 指令特性参数：[`track-by`](/guide/list.html#使用_track-by), [`stagger`](/guide/transitions.html#渐进过渡效果), [`enter-stagger`](/guide/transitions.html#渐进过渡效果), [`leave-stagger`](/guide/transitions.html#渐进过渡效果)

为绑定的数组或对象中的每一项创建一个子 ViewModel 实例。如果值是一个数字，则会创建对应数量的子实例。当数组或对象的可变方法 (mutating method) 比如 `push()` 被调用，或是绑定的数字变化时，相应的子实例都会自动被创建或删除。

如果没有提供参数，子实例会直接使用数组内分配的元素作为其 `$data`。如果值不是一个对象，则会将其作为 `$value` 直接存放在实例上。

**示例:**

``` html
<ul>
  <li v-repeat="users">
    {{name}} {{email}}
  </li>
</ul>
```

如果提供了参数，则总是会创建一个数据包装对象，用参数字符串作为键名来存放原本的数据对象。别名参数可以使得模板中的数据访问更加清晰：

``` html
<ul>
  <li v-repeat="user : users">
    {{user.name}} {{user.email}}
  </li>
</ul>
```

0.12.8 引入了一种更自然的别名语法：

``` html
<ul>
  <li v-repeat="user in users">
    {{user.name}} {{user.email}}
  </li>
</ul>
```

更多详细示例，请参看[列表渲染](../guide/list.html).

## 字面指令

> 字面指令将它们的特性值视为纯字符串，不会视图建立数据绑定。它们只负责调用 `bind()`函数一次。字面指令的值也可以包含 mustache 表达式插值，具体的处理方式请参考[动态字面指令](/guide/custom-directive.html#动态字面指令)。

### v-transition

- 可以响应 Mustaches 插值

通知 Vue.js 为元素应用过渡效果。当一个可以触发过渡效果的指令将元素插入或移除时，或是当 Vue 实例中操作 DOM 的方法被调用时，对应的过渡效果就会被应用到该元素上。

详情请见[过渡系统](/guide/transitions.html)。

### v-ref

在父组件上注册一个引用到子组件，便于访问只能和组件或是 `v-repeat` 协同使用。注册之后，父级的 `$` 对象上可以访问注册的子实例。示例见[子组件引用](../guide/components.html#子组件引用).

当该指令与 `v-repeat` 一起使用时，`v-ref` 注册的值将会是一个包含了所有子实例的数组，这个数组与 `v-repeat` 绑定的数组是相对应的。

如果 `v-repeat` 的源数据是一个对象，则 `v-ref` 注册的值会是一个对象，按照键值包含对应的子实例。

### v-el

在 Vue 实例的 `$$` 对象里注册一个 DOM 元素的引用。比如 `<div v-el="hi">` 可以使用`vm.$$.hi` 获取。

## 空指令

> 空指令不需要参数，并且会忽略它的特性值。

### v-pre

跳过编译此元素和此元素所有的子元素。跳过大量没有指令的节点可以加快编译速度。

### v-cloak

直到关联的 ViewModel 结束编译之前本属性都会留在元素上。与 `[v-cloak] { display: none }` 类似的样式结合，这个指令可以用来在 ViewModel 准备好之前隐藏没有被编译的 &#123;&#123; Mustache &#125;&#125; 模板。

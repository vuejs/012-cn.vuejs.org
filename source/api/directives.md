title: 指令
type: api
order: 6
---

## 响应式指令 
 
> Directive 可以将自己与一个 Vue 实例的属性绑定，也可以与一个运行在实例上下文中的表达式绑定。当底层属性或表达式的值发生改变时，指令的`update()`方法会在下个瞬间异步地调用。

### v-text

更新元素的`textContent`

本质上来说，{{ Mustache }} 的插值也被当做文字节点上的`v-text`指令进行编译。

### v-html

更新元素的`innerHTML`

  在用户产生的数据中使用`v-html`比较危险。使用`v-html`时应确保数据的安全性，或通过自定义过滤器将不被信任的 HTML 内容进行处理。

### v-show

- 本指令可以触发动画效果。

根据绑定的实际值将元素的 display 属性设置为`none`或它的原始值。

### v-class

- 本指令接受配置参数

如果没有提供参数，则将绑定值加入到元素的类列表classList 中，当绑定值改变时更新class.
 
如果提供了参数，则会根据参数去切换类绑定值所依赖的实际值。多条合并使用很方便：

``` html
<span v-class="
  red    : hasError,
  bold   : isImportant,
  hidden : isHidden
"></span>
```

或者，你可以直接将该指令绑定到一个对象。对象关键词keys将使类列根据对应值予以切换。 

### v-attr

- 本指令需要一个参数

更新元素的指定属性（由参数指定）

**例子:**

``` html
<canvas v-attr="width:w, height:h"></canvas>
```

除0外的其他伪值将移除该属性.

或者，你可以直接将该指令绑定到一个对象。对象关键词keys将使属性列根据对应值予以设定。

本质上，普通特性中的 {{ Mustache }} 插值会被编译到计算后的`v-attr`指令当中。

从0.12.8开始，若设定（property）存在，`v-attr` 也设置（set）相应设定。例如，<input value="{% raw %}{{val}}{% endraw %}"> 将不仅更新属性，也设置设定值。如果元素绑定的属性没有相应的设定，则不会去设置。
 
<p class="tip">在为`<img>`元素设置`src`属性时，应该使用`v-attr`绑定而不是 mustache 模板绑定。浏览器会先于 Vue.js 对你的模板进行解析。所以当浏览器试图获取图片 URL 时，使用 mustache 模板绑定的数据会导致404错误。</p>

### v-style

- 本指令接受一个配置参数

将CSS属性以内联的形式应用到元素上。

如果没有参数名，绑定值也可以是一个字符串或者一个对象。

- 如果参数值为字符串，则会将该元素的`style.cssText`属性设置为参数的值。
- 如果参数值为对象，则每一对 key/value 都会被设置到元素的`style`对象上。

**例子:**

``` html
<div v-style="myStyles"></div>
```

``` js
// myStyles can either be a String:
"color:red; font-weight:bold;"
// or an Object:
{
  color: 'red',
  // both camelCase and dash-case works
  fontWeight: 'bold',
  'font-size': '2em'
}
```

如果有参数的话，参数会被当作 CSS 设定来用。可以通过合并多个参数的方式同时设置多个设定。

**例子:**

``` html
<div v-style="
  top: top + 'px',
  left: left + 'px',
  background-color: 'rgb(0,0,' + bg + ')'
"></div>
```

`v-style`也可以智能的查找 CSS 属性是否需要浏览器前缀，所以你可以放心的使用无前缀版本的 CSS 属性。

``` html
<!-- will use -webkit-transform if needed, for example -->
<div v-style="transform: 'scale(' + scale + ')'"></div>
```

因为 IE 浏览器的原因，这里推荐使用`v-style`指令来代替直接在 style 属性中使用 &#123;&#123;mustache&#125;&#125; 插值，这是因为在 IE 的所有版本中，都会在解析 HTML 时将非法的内联样式删除掉。

### v-on

- 本指令需要一个参数。
- 本指令所需的值应该是一个函数或者声明。

为元素添加一个事件监听器。事件的类型由参数来表示。这也是唯一可以和`key`过滤器一起使用的指令。详细请见[Listening for Events](../guide/events.html)。

### v-model

- 本指令只能用在`<input>`, `<select>` 或 `<textarea>` 元素上。
- 指令的参数有: [`lazy`](/guide/forms.html#Lazy_Updates), [`number`](/guide/forms.html#Casting_Value_as_Number), [`options`](/guide/forms.html#Dynamic_Select_Options), [`debounce`](/guide/forms.html#Input_Debounce)

在表单输入元素上创建双向绑定，默认情况下，每一个`input`事件都会让数据同步。详情请见[控制表单](../guide/forms.html).

### v-if

- 本指令可以触发动画效果。

基于绑定值的实际值为条件插入或移除元素。如果元素为`<template>`元素，则它的内容将会被提取出来作为条件块。

**例子:**

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

- 本指令将创建 子Vue 实例。
- 本指令需要的值应为一个数组、对象或数字。
- 本指令可以触发动画效果。
- 本指令可接受一个配置参数。
- 指令的参数：[`track-by`](/guide/list.html#Using_track-by), [`stagger`](/guide/transitions.html#Staggering_Transitions), [`enter-stagger`](/guide/transitions.html#Staggering_Transitions), [`leave-stagger`](/guide/transitions.html#Staggering_Transitions)

为每一个绑定的数组或对象中的项创建一个子 ViewModel 。如果值是整数，则创建多个子 ViewModel 。当数组或对象的可变方法（ mutating method ）被调用，如`push()`方法，或者当数字值有增加或减少时，子 ViewModel 都会自动被创建或删除。

如果没有提供参数，子 ViewModel 会直接使用数组内分配的元素作为`$data`。如果值不是一个对象，则会创建一个数据包装对象，而值会被设置在key的别名为`$value`的对象上。

**例子:**

``` html
<ul>
  <li v-repeat="users">
    {{name}} {{email}}
  </li>
</ul>
```

如果提供了参数，则总是会创建一个数据包装对象，用参数字符串作为别名key. 这使得模板中的设定访问更加明确。

``` html
<ul>
  <li v-repeat="user : users">
    {{user.name}} {{user.email}}
  </li>
</ul>
```

从0.12.8起，有了一个特别的选择能让语法更自然。

``` html
<ul>
  <li v-repeat="user in users">
    {{user.name}} {{user.email}}
  </li>
</ul>
```

查看详细的例子，请看[展示一个列表](../guide/list.html).

## 字面指令

> 字面指令将它的属性值当成纯字符串来处理。字面指令不把自己绑定到任何东西上。它们只将字符串值传入到`bind()`函数中执行一次。字面指令的值接受 &#123;&#123;mustache&#125;&#125; 表达式，但这个表达式只在首次编译时执行一次，不再响应数据变化。

### v-transition

- 可以响应mustaches表达式

- 通知 Vue.js 为元素应用动画效果。当某一动画触发指令改变了该元素时，或当 Vue 实例中操作 DOM 的方法被调用时，该动画类被应用到元素上。

详情请见[动画器指南](/guide/transitions.html).

### v-ref

为了让父级更加方便的访问子级，可以在父级注册一个子组件的引用。本指令只能被用于一个component组件，或与`v-repeat`一起使用。在它父级的`$`对象上可以访问组件的实例。例子在[子引用](../guide/components.html#子组件引用).

当该指令与`v-repeat`一起使用时，`v-ref`的值将会是一个包含了所有子 Vue 实例的数组，这个数组与子 Vue 实例绑定的数组是相对应的。

0.12版的更新: 如果`v-repeat`的来源数据是一个对象，则`v-ref`将返回一个包含匹配该对象中每个key的实例集的对象。

### v-el

在一个DOM元素上注册一个更容易被自身 Vue 实例访问的引用。如， `<div v-el="hi">`可以使用`vm.$$.hi`访问到。

## 空指令

> 空指令不需要参数，并且会忽略它的属性值。

### v-pre

跳过编译此元素和此元素所有的子元素。跳过大量没有指令的节点可以加快编译速度。

### v-cloak

直到关联的 ViewModel 结束编译之前本属性都会留在元素上。与 `[v-cloak] { display: none }` 类似的样式结合，这个指令可以用来在 ViewModel 准备好之前隐藏没有被编译的 &#123;&#123; Mustache &#125;&#125; 模板。

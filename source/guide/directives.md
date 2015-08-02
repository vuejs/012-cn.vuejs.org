title: 指令
type: 教程
order: 3
---

## 概要

如果你没有用过 AngularJS，你可能不太清楚指令 (directive) 是什么。一个指令的本质是模板中出现的特殊标记，让处理模板的库知道需要对这里的 DOM 元素进行一些对应的处理。Vue.js 的指令概念相比 Angular 要简单得多。Vue.js 中的指令只会以带前缀的 HTML 特性 (attribute) 的形式出现：

``` html
<element
  prefix-directiveId="[argument:] expression [| filters...]">
</element>
```

## 简单示例

``` html
<div v-text="message"></div>
```

这里的前缀是默认的 `v-`。指令的 ID 是 `text`，表达式是 `message`。这个指令告诉 Vue.js， 当 Vue 实例的 `message` 属性改变时，更新该 div 元素的 `textContent`。

## 内联表达式

``` html
<div v-text="'hello ' + user.firstName + ' ' + user.lastName"></div>
```

这里我们使用了一个计算表达式 (computed expression)，而不仅仅是简单的属性名。Vue.js 会自动跟踪表达式中依赖的属性并在这些依赖发生变化的时候触发指令更新。同时，因为有异步批处理更新机制，哪怕多个依赖同时变化，表达式也只会触发一次。

你应该明智地使用表达式，并避免在你的模板里放入过多的逻辑，尤其是有副作用的语句 (事件监听表达式除外)。为了防止在模板内滥用逻辑，Vue.js 把内联表达式限制为**一条语句**。如果需要绑定更复杂的操作，请使用 [计算属性](../guide/computed.html)。

<p class="tip">出于安全考虑，在内联表达式中你只能访问当前上下文中 Vue 实例的属性和方法。</p>

## 参数

``` html
<div v-on="click : clickHandler"></div>
```

有些指令需要在路径或表达式前加一个参数。在这个例子中 `click` 参数代表了我们希望 `v-on` 指令监听到点击事件之后调用该 ViewModel 实例的 `clickHandler` 方法。

## 过滤器

过滤器可以紧跟在指令的路径或表达式之后，在更新 DOM 之前对值进行进一步处理。过滤器像 shell 脚本一样跟在一个管道符 (`|`) 之后。更多内容请移步至 [深入了解过滤器](../guide/filters.html)。

## 多重指令从句

你可以在同一个特性里多次绑定同一个指令。这些绑定用逗号分隔，它们在底层被分解为多个指令实例进行绑定。

``` html
<div v-on="
  click   : onClick,
  keyup   : onKeyup,
  keydown : onKeydown
">
</div>
```

## 字面量指令

有些指令不会创建数据绑定——它们的值只是一个字符串字面量。比如 `v-ref` 指令：

``` html
<my-component v-ref="some-string-id"></my-component>
```

这里的 `"some-string-id"` 并不是一个反应式的表达式 — Vue.js不会尝试去观测组件中的对应数据。

在有些情况下，你也可以使用 Mustache 风格绑定来使得字面量指令 “反应化”：

``` html
<div v-show="showMsg" v-transition="{{dynamicTransitionId}}"></div>
```

但是，请注意只有 `v-transition` 指令具有此特性。Mustache 表达式在其他字面量指令中，例如 `v-ref` 和 `v-el`，只会被计算**一次**。它们在编译完成后将不会再响应数据的变化。

完整的字面量指令列表可以在 [API 索引](../api/directives.html#字面指令) 中找到。

## 空指令

有些指令并不需要判断特性的值 —— 这些操作对某个元素处理且仅处理一次。比如 `v-pre` 指令：

``` html
<div v-pre>
  <!-- 内部模板将不会被编译 -->
</div>
```

完整的空指令列表可以在 [API 索引](../api/directives.html#空指令) 中找到。

下一步：[过滤器](../guide/filters.html)。

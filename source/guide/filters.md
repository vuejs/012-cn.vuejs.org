title: 过滤器
type: 教程
order: 4
---

## 概要

一个 Vue.js 的过滤器本质上是一个函数，这个函数会接收一个值，将其处理并返回。过滤器在指令中由一个管道符 (`|`) 标记，并可以跟随一个或多个参数：

``` html
<element directive="expression | filterId [args...]"></element>
```

## 示例

过滤器必须放置在一个指令的值的最后：

``` html
<span v-text="message | capitalize"></span>
```

你也可以用在 mustache 风格的绑定的内部：

``` html
<span>{{message | uppercase}}</span>
```

可以串联多个过滤器：

``` html
<span>{{message | lowercase | reverse}}</span>
```

## 参数

一些过滤器是可以接受参数的。参数用空格分隔开：

``` html
<span>{{order | pluralize 'st' 'nd' 'rd' 'th'}}</span>
```

``` html
<input v-on="keyup: submitForm | key 'enter'">
```

纯字符串参数需要用引号包裹。无引号的参数会作为表达式在当前数据作用域内动态计算。在后面讲到自定义过滤器时，我们进一步讨论这些细节。

上述示例的具体用法参见 [完整的内建过滤器列表](../api/filters.html)。

现在你已经了解了指令和过滤器，接下来我们趁热打铁，看看如何 [展示一个列表](../guide/list.html)。

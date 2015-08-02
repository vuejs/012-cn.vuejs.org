title: 过滤器
type: 教程
order: 4
---

## 摘要

一个 Vue.js 的过滤器本质上是一个函数，这个函数会接收一个值，将其处理并返回。它被标记在一个竖线 (`|`) 之后，并可以跟随一个或多个参数：

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

可以把多个过滤器链在一起：

``` html
<span>{{message | lowercase | reverse}}</span>
```

## 参数

一些过滤器是可以附带配置参数的。参数用空格分隔开：

``` html
<span>{{order | pluralize 'st' 'nd' 'rd' 'th'}}</span>
```

``` html
<input v-on="keyup: submitForm | key 'enter'">
```

原始的字符串参数需要用单引号围住。无引号的参数在当前数据范围内被认定是动态的。以后在说到自定义过滤器时，我们会仔细讨论这个问题的细节。

上述示例的具体用法参见[完整的内建过滤器列表](../api/filters.html)。

现在你已经了解了指令和过滤器，接下来我们趁热打铁[展示一个列表](../guide/list.html)吧。

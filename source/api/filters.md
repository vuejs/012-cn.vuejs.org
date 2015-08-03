title: 过滤器
type: api
order: 7
---

### capitalize

*'abc' => 'Abc'*

### uppercase

*'abc' => 'ABC'*

### lowercase

*'ABC' => 'abc'*

### currency

- 接受一个可选参数

*12345 => $12,345.00*

你可以传递一个可选参数来表示货币符号（默认为 $）。

### pluralize

- 要求至少一个参数

将值转为复数。如果只有一个参数，复数形式只是在末尾添加一个 “s”。如果有多个参数，参数列表对应于 1 个，2 个，3 个 … 。如果参数的个数不够，将使用最后一项。

**示例：**

``` html
{{count}} {{count | pluralize 'item'}}
```

*1 => '1 item'*  
*2 => '2 items'*

``` html
{{date}}{{date | pluralize 'st' 'nd' 'rd' 'th'}}
```

结果为:

*1 => '1st'*  
*2 => '2nd'*
*3 => '3rd'*
*4 => '4th'*
*5 => '5th'*

### json

- 接受一个可选参数

使用 `JSON.stringify()` 来处理传入的值，而不是直接将其字符串化（如 `[object Object]`)。接受一个可选参数来指定缩进级别（默认是 2）。

``` html
<pre>{{$data | json 4}}</pre>
```

### key

- 只能和 `v-on` 指令协同使用
- 要求至少一个参数

包装事件处理器，只有当 keyCode 等于参数时才调用它。对于一些常用的键也可以用字符串形式的别名：

- enter
- tab
- delete
- esc
- up
- down
- left
- right

**示例：**

``` html
<input v-on="keyup:doSomething | key 'enter'">
```

只有按回车键（enter）时才会调用 `doSomething`。

### filterBy

**语法：** `filterBy searchKey [in dataKey]`.

- 只能和 `v-repeat` 指令协同使用

使得 `v-repeat` 只显示数组过滤后的结果。`searchKey` 参数是当前 ViewModel 的一个属性名。这个属性的值会被用作查找的目标：

``` html
<input v-model="searchText">
<ul>
  <li v-repeat="users | filterBy searchText">{{name}}</li>
</ul>
```

当使用此过滤器时，将递归遍历 `users` 数组的每一个元素来寻找 `searchText` 的当前值从而过滤该数组。举例来说，如果一个条目是 `{ name: 'Jack', phone: '555-123-4567' }`，而 `searchText` 的值是 `'555'`，这个条目就被认为是符合条件。

你也可以通过可选的 `in dataKey` 参数来指定具体要在哪个属性中进行查找：

``` html
<input v-model="searchText">
<ul>
  <li v-repeat="users | filterBy searchText in name">{{name}}</li>
</ul>
```

现在只有当 `name` 属性包含 `searchText` 时这个条目才符合条件。所以当 `searchText` 的值是 `'555'` 时这个条目将不符合条件，而当值是 `'Jack'` 时则符合。

最后，你可以使用引号来表示字面量参数：

``` html
<ul>
  <li v-repeat="users | filterBy '555' in 'phone'">{{name}}</li>
</ul>
```

### orderBy

**语法：** `orderBy sortKey [reverseKey]`.

- 只能和 `v-repeat` 指令协同使用

将 `v-repeat` 的结果排序。`sortKey`参数是当前 ViewModel 的一个属性名。这个属性的值表示用来排序的键名。可选的 `reverseKey` 参数也是当前 ViewModel 的一个属性名，如果这个属性值为真则数组会被倒序排列。

``` html
<ul>
  <li v-repeat="users | orderBy field reverse">{{name}}</li>
</ul>
```

``` js
new Vue({
  /* ... */
  data: {
    field: 'name',
    reverse: false
  }
})
```

你可以使用引号来表示字面量的排序键名。使用 `-1` 来表示字面量的 `reverse` 参数。

``` html
<ul>
  <li v-repeat="users | orderBy 'name' -1">{{name}}</li>
</ul>
```

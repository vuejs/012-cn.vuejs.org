title: 处理表单
type: guide
order: 7
---

## 基本用法

你可以在表单的 input 元素上使用 `v-model` 指令来创建双向数据绑定。它会根据 input type 自动选取正确的方式更新元素。

**示例**

``` html
<form id="demo">
  <!-- text -->
  <p>
    <input type="text" v-model="msg">
    {{msg}}
  </p>
  <!-- checkbox -->
  <p>
    <input type="checkbox" v-model="checked">
    {{checked ? "yes" : "no"}}
  </p>
  <!-- radio buttons -->
  <p>
    <input type="radio" name="picked" value="one" v-model="picked">
    <input type="radio" name="picked" value="two" v-model="picked">
    {{picked}}
  </p>
  <!-- select -->
  <p>
    <select v-model="selected">
      <option>one</option>
      <option>two</option>
    </select>
    {{selected}}
  </p>
  <!-- multiple select -->
  <p>
    <select v-model="multiSelect" multiple>
      <option>one</option>
      <option>two</option>
      <option>three</option>
    </select>
    {{multiSelect}}
  </p>
  <p><pre>data: {{$data | json 2}}</pre></p>
</form>
```

``` js
new Vue({
  el: '#demo',
  data: {
    msg      : 'hi!',
    checked  : true,
    picked   : 'one',
    selected : 'two',
    multiSelect: ['one', 'three']
  }
})
```

**效果**

<form id="demo"><p><input type="text" v-model="msg"> {&#123;msg&#125;}</p><p><input type="checkbox" v-model="checked"> {&#123;checked ? &quot;yes&quot; : &quot;no&quot;&#125;}</p><p><input type="radio" v-model="picked" name="picked" value="one"><input type="radio" v-model="picked" name="picked" value="two"> {&#123;picked&#125;}</p><p><select v-model="selected"><option>one</option><option>two</option></select> {&#123;selected&#125;}</p><p><select v-model="multiSelect" multiple><option>one</option><option>two</option><option>three</option></select>{&#123;multiSelect&#125;}</p><p>data:<pre style="font-size:13px;background:transparent;line-height:1.5em">{&#123;$data | json 2&#125;}</pre></p></form>
<script>
new Vue({
  el: '#demo',
  data: {
    msg      : 'hi!',
    checked  : true,
    picked   : 'one',
    selected : 'two',
    multiSelect: ['one', 'three']
  }
})
</script>

## 懒更新

默认情况下，`v-model` 会在每个 `input` 事件之后同步输入的数据。你可以添加一个 `lazy` 特性，将其改变为在 `change` 事件之后才进行同步。

``` html
<!-- synced after "change" instead of "input" -->
<input v-model="msg" lazy>
```

## 转换为数字

如果你希望用户的输入自动处理为一个数字，你可以在 `v-model` 所在的 input 上添加一个 `number` 特性。

``` html
<input v-model="age" number>
```

## 动态 select 选项

当你需要为一个 `<select>` 元素动态渲染列表选项时，我们推荐 `options` 和 `v-model` 特性配合使用，这样当选项动态改变时，`v-model`会正确地同步：

``` html
<select v-model="selected" options="myOptions"></select>
```

在你的数据里，`myOptions` 应该是一个指向选项数组的路径/表达式。

该数组可以包含普通字符串或对象。对象的格式应为 `{text:'', value:''}`。这允许你把展示的文字和其背后对应的值区分开来。

``` js
[
  { text: 'A', value: 'a' },
  { text: 'B', value: 'b' }
]
```

会渲染成：

``` html
<select>
  <option value="a">A</option>
  <option value="b">B</option>
</select>
```

另外，数组里对象的格式也可以是 `{label:'', options:[...]}`。这样的数据会被渲染成为一个 `<optgroup>`：

``` js
[
  { label: 'A', options: ['a', 'b']},
  { label: 'B', options: ['c', 'd']}
]
```

会渲染成：

``` html
<select>
  <optgroup label="A">
    <option value="a">a</option>
    <option value="b">b</option>
  </optgroup>
  <optgroup label="B">
    <option value="c">c</option>
    <option value="d">d</option>
  </optgroup>
</select>
```

有很大可能，你接入的源数据并非期望的格式，你必须转换数据来生成动态选项列。为了尽量消除这种转换，`options` 参数支持过滤器，对将你的转换逻辑做成一个可复用的[定制过滤器](/guide/custom-filter.html)很有帮助：

``` js
Vue.filter('extract', function (value, keyToExtract) {
  return value.map(function (item) {
    return item[keyToExtract]
  })
})
```

``` html
<select options="users | extract 'name'"></select>
```

上述过滤器将像`[{ name: 'Bruce' }, { name: 'Chuck' }]`这样的数据转化为`['Bruce', 'Chuck']`，它变成了正确的格式。

## 输入去抖动

在一次输入被同步到模型model之前，`debounce`参数允许你设置一个每次按键后的最低延迟。当每次更新前你要执行繁重作业时会很有用，例如一个ajax请求来进行提前键入的自动完成。

``` html
<input v-model="msg" debounce="500">
```

**结果**

<div id="debounce-demo" class="demo">{&#123;msg&#125;}<br><input v-model="msg" debounce="500"></div>
<script>
new Vue({
  el:'#debounce-demo',
  data: { msg: 'edit me' }
})
</script>

注意 `debounce` 参数不能对用户的输入事件去抖动：它对底层数据的“写入“操作进行去抖动。因此当使用`debounce`时，你应该用`vm.$watch()`来响应数据变化。

接下来：[可推导的属性](../guide/computed.html).

title: 创建大型应用
type: 教程
order: 13
---

Vue.js 在设计思想上追求的是尽可能的灵活。它本身只是一个界面库，并不强制使用哪种架构。这对于快速原型开发很有用，但是对于经验欠缺的开发者，用 Vue.js 构建大型应用可能会是一个挑战。在这里我会针对在使用 Vue.js 时如何组织大型的项目提供一些略带个人偏好的建议。

## 模块化

虽然独立构建的 Vue.js 可以被用作一个全局变量，但是它通常更适合配合一个模块化构建系统使用，这可以让你更好地组织代码。我的建议是用 CommonJS 模块格式编写源代码 (这是 Node.js 使用的格式，也是 Vue.js 源代码使用的格式)，并通过 [Webpack](http://webpack.github.io/) 或 [Browserify](http://browserify.org/) 把它们打包起来。

更重要的是，Webpack 和 Browserify 不仅仅是模块打包工具。两者都提供源码转换的 API，允许你将你的源码用其他的预处理程序进行转换。例如，你可以用 [babel-loader](https://github.com/babel/babel-loader) 或 [babelify](https://github.com/babel/babelify) 直接在你的模块中使用 ES6/7 的语法。

## 单文件组件

在一个典型的 Vue.js 项目里，我们会希望将我们的代码拆分成许多的组件，并且在一个组件里把它所包含的 CSS 样式、HTML 模板、JavaScript 逻辑封装在一起。就像上面提到的那样，借助 Webpack 或 Browserify，并配以相应的源码转换插件，我们就可以这样编写组件了：

![](../images/vue-component.png)

如果你喜欢预处理器，你甚至可以这样写：

![](../images/vue-component-with-pre-processors.png)

你可以用 Webpack + [vue-loader](https://github.com/vuejs/vue-loader) 或 Browserify + [vueify](https://github.com/vuejs/vueify) 来编译这些单文件的 Vue 组件。如果你同时使用预处理器，则推荐用 Webpack 来进行构建，因为 Webpack 的插件 API 提供了更好的文件依赖追踪和缓存支持。

在 GitHub 上可以找到上面所描述的工作流的代码示例：

[Webpack + vue-loader](https://github.com/vuejs/vue-loader-example)
[Browserify + vueify](https://github.com/vuejs/vueify-example)

## 路由

<p class="tip">官方路由模块 `vue-router` 正在活跃开发中，即将发布。</p>

你可以手动监听 hashchange 事件并利用一个动态的 `<component>` 来实现一些基本的路由逻辑。

**示例：**

``` html
<div id="app">
  <component is="{{currentView}}"></component>
</div>
```

``` js
Vue.component('home', { /* ... */ })
Vue.component('page1', { /* ... */ })
var app = new Vue({
  el: '#app',
  data: {
    currentView: 'home'
  }
})
// Switching pages in your route handler:
app.currentView = 'page1'
```

利用这种机制我们可以很容易地接入独立的路由库，比如 [Page.js](https://github.com/visionmedia/page.js) 或是 [Director](https://github.com/flatiron/director)。

## 服务器通信

所有的 Vue 实例都可以直接通过 `JSON.stringify()` 序列化得到它们原始的 `$data`，并不需要做任何额外的工作。社区已经贡献了 [vue-resource](https://github.com/vuejs/vue-resource) 插件，提供了与 RESTful API 协作的功能。你也可以使用任何你喜欢的 Ajax 组件，比如 `$.ajax` 或是 [SuperAgent](https://github.com/visionmedia/superagent)。Vue.js 也可以和诸如 Firebase 和 Parse 这样的 BaaS 服务完美配合。

## 单元测试

任何兼容 CommonJS 构建系统的测试工具都可以。建议使用 [Karma](http://karma-runner.github.io/0.12/index.html) 配合其 [CommonJS预处理插件](https://github.com/karma-runner/karma-commonjs) 对你的代码进行模块化测试。

最佳实践是暴露出模块内的原始选项/函数。参考如下示例：

``` js
// my-component.js
module.exports = {
  template: '<span>{{msg}}</span>',
  data: function () {
    return {
      msg: 'hello!'
    }
  }
  created: function () {
    console.log('my-component created!')
  }
}
```

你可以在你的入口模块中如下使用这个文件：

``` js
// main.js
var Vue = require('vue')
var app = new Vue({
  el: '#app',
  data: { /* ... */ },
  components: {
    'my-component': require('./my-component')
  }
})
```

然后你可以如下测试该模块：

``` js
// Some Jasmine 2.0 tests
describe('my-component', function () {  
  // require source module
  var myComponent = require('../src/my-component')
  it('should have a created hook', function () {
    expect(typeof myComponent.created).toBe('function')
  })
  it('should set correct default data', function () {
    expect(typeof myComponent.data).toBe('function')
    var defaultData = myComponent.data()
    expect(defaultData.message).toBe('hello!')
  })
})
```

<p class="tip">因为 Vue.js 的指令异步响应数据的更新，当你需要在数据更新后断言 DOM 的状态时，你需要在一个 `Vue.nextTick` 回调里做这件事。</p>


## 发布至生产环境

为了缩小体积，最小化后的独立版 Vue.js 已去除所有的警告信息，但当你用像 Browserify、Webpack 这样的工具构建 Vue.js 应用时，并没有一个明显的办法来去除这些警告。

从 0.12.8 开始，可以采用如下的方式配置你的构建工具来优化最后生成的代码体积：

### Webpack

使用 Webpack 的 [DefinePlugin](http://webpack.github.io/docs/list-of-plugins.html#defineplugin) 来定义生产环境参数，这样警告相关的代码会变得永远不会被执行，从而在 UglifyJS 压缩的时候会被自动丢掉。比如：

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: '"production"'
      }
    }),
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      }
    })
  ]
}
```

### Browserify

只需要在打包命令中把 `NODE_ENV` 设置成 `"production"` 即可。Vue 会自动应用 [envify](https://github.com/hughsk/envify) 转换并跳过警告处理。比如：

``` bash
NODE_ENV=production browserify -e main.js | uglifyjs -c -m > build.js
```

## 应用示例

[Vue.js Hackernews Clone](https://github.com/yyx990803/vue-hackernews) 是一个应用的例子，它用 Webpack + vue-loader 代码组织、Director.js做路由、HackerNews 官方的 Firebase API 为后端。这不算什么特别大的应用，但它结合并展示了本页面讨论到的各方面概念。

下一节：[扩展 Vue](../guide/extending.html)

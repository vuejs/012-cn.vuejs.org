title: 常见问题
type: 教程
order: 16
---

- **为什么 Vue.js 不支持 IE8?**

  Vue.js 借助 ECMASCript 5 的 `Object.defineProperty` 才得以不靠脏检查实现原生对象即模型的 API。然而这一新特性在 IE8 里只在 DOM 元素上起作用，对原生 JavaScript 对象无效，而且无法通过 polyfill 来修正。

- **那么 Vue.js 修改了我的数据喽?**

  是，也不是。Vue.js 只是将正常属性转化为 getters 和 setters，这样它才能在属性被访问或更改时得到通知。而在序列化数据时，结果是完全相同的。当然，这里有一些注意事项：

  1. 当你使用 `console.log` 观察对象时你将只能看到一串 getter/setters. 但你可以使用 `vm.$log()` 来打印出一个更直观的输出。

  2. 你不能在数据对象里定义自己的 getter/setters。这通常来说不是问题，因为数据对象应该是从纯 JSON 中解析出来的，而且 Vue.js 提供了计算属性功能。

  3. Vue.js 在被观察的对象上添加了一些拓展的属性或方法，比如 `__ob__`, `$add`, `$set` 以及 `$delete`。这些属性是不可遍历的，所以它们不会显示在 `for ... in ...` 循环中。只要你不覆盖它们，就不会有什么问题。

  除此之外，访问属性和赋值都和原生对象一样, `JSON.stringify` 和 `for ... in ...` 循环也能够照常运行。99.9% 的情况下你都不需要考虑这些问题。

- **Vue.js 目前的状态怎样? 我能把它应用在生产环境中么?**

  Vue.js 最新的 0.12 发布版本，经历了一些API变更，但这是 1.0 版本前的最后一个计划发布版本。与此同时，全球各地都已经有公司/产品将 Vue.js 使用在了生产环境中。Vue.js 的测试覆盖率和 bug 修复速度也在同类框架中是首屈一指的。参见：[使用了 Vue.js 的项目（不完全列表）](https://github.com/yyx990803/vue/wiki/Projects-Using-Vue.js)。

- **Vue.js 是完全免费的吗?**

  Vue.js 基于 MIT 协议发布，是完全开源且免费的。

- **Vue.js 和 AngularJS 之间的区别是什么?**

  下面是一些选择 Vue 而不是 Angular 的可能原因，当然它们可能不适用于每一个人：

  1. Vue.js 是一个更加灵活开放的解决方案。它允许你以希望的方式组织你的应用程序，而不是任何时候都必须遵循 Angular 制定的规则。它仅仅是一个视图层，所以你可以将它嵌入一个现有页面而不一定要做成一个庞大的单页应用。在结合其他库方面它给了你更大的的空间，但相应，你也需要做更多的架构决策。例如，Vue.js核心默认不包含路由和 ajax 功能，并且通常假定你在用应用中使用了一个外部的模块构建系统。这可能是最重要的区别。

  2. 在 API 和内部设计方面，Vue.js 比 Angular 简单得多, 因此你可以快速地掌握它的全部特性并投入开发。

  3. Vue.js 拥有更好的性能，因为它不使用脏检查。当 watcher 越来越多时, Angular 会变得越来越慢，因为作用域内的每一次数据变更，所有的 watcher 都需要被重新求值。Vue 则根本没有这个问题，因为它采用的是基于依赖追踪的观察系统，所以所有的数据变更触发都是独立的，除非它们之间有明确的依赖关系。

  4. Vue.js 中指令和组件的概念区分得更为清晰。指令只负责封装 DOM 操作，而组件代表一个自给自足的独立单元 —— 它拥有自己的视图和数据逻辑。在 Angular 中它们两者间有不少概念上的混淆。

  当然，需要指出的是 Vue.js 还是一个相对年轻的项目，而 Angular 经受过大量的实战检验，并且背后有谷歌支持，还有一个更大的社区。

- **Vue.js 和 React.js 有什么区别?**

  React.js 和 Vue.js 确实有一些相似 —— 它们都提供数据驱动、可组合搭建的视图组件。然而，它们的内部实现是完全不同的。React 是基于  Virtual DOM —— 一种在内存中描述 DOM 树状态的数据结构。React 中的数据通常被看作是不可变的，而 DOM 操作则是通过 Virtual DOM 的 diff 来计算的。与之相比，Vue.js 中的数据默认是可变的，而数据的变更会直接出发对应的 DOM 更新。相比于 Virtual DOM，Vue.js 使用实际的 DOM 作为模板，并且保持对真实节点的引用来进行数据绑定。

  Virtual DOM 提供了一个函数式的描述视图的方法，这很 cool。因为它不使用数据观察机制，每次更新都会重新渲染整个应用，因此从定义上保证了视图通与数据的同步。它也开辟了 JavaScript 同构应用的可能性。

  实话实说，我自己对 React 的设计理念也是十分欣赏的。但 React 有一个问题就是组件的逻辑和视图结合得非常紧密。对于部分开发者来说，他们可能觉得这是个优点，但对那些像我一样兼顾设计和开发的人来说，还是更偏好模板，因为模板能让我们更好地在视觉上思考设计和 CSS。JSX 和 JavaScript 逻辑的混合干扰了我将代码映射到设计的思维过程。相反，Vue.js 通过在模板中加入一个轻量级的DSL (指令系统)，换来一个依旧直观的模板，且能够将逻辑封装进指令和过滤器中。

  React的另一个问题是：由于 DOM 更新完全交由 Virtual DOM 管理，当你真的**想要**自己控制 DOM 是就有点棘手了（虽然理论上你可以，但这样做时你本质上在对抗 React 的设计思想）。对于需要复杂时间控制的动画来说这就变成了一项很讨人厌的限制。在这方面，Vue.js 允许更多的灵活性，并且有不少用 Vue.js 构建的富交互实例。参见[一些用 Vue.js 制作的 FWA/Awwwards 获奖站点](https://github.com/yyx990803/vue/wiki/Projects-Using-Vue.js#interactive-experiences)
  
- **Vue.js 和 Polymer 有什么区别?**

  Polymer 是另一个由 Google 支持的项目，实际上也是 Vue.js 的灵感来源之一。Vue.js 的组件可以类比为 Polymer 中的自定义元素，它们都提供类似的开发体验。最大的不同在于，Polymer 依赖最新的 Web Components 特性，在不支持的浏览器中，需要加载笨重的 polyfill，性能也会受到影响。相对的，Vue.js 无需任何依赖，最低兼容到IE9。

- **Vue.js 和 KnockoutJS 有什么区别?**

  首先，Vue 提供了一个更清晰的语法来获取/设置 VM 属性。

  在更高层面上，Vue 与 Knockout 的区别是，Vue 的组件系统鼓励你使用 自上而下、结构优先、声明式的设计策略，而不是从下而上命令式的创建 ViewModel。Vue 的源数据是一个纯对象，不包含逻辑，可以直接 JSON.stringify 并传给 post 请求。ViewModel 代理了数据对象。Vue 实例始终用原生数据绑定相应的 DOM 元素。Knockout 的 ViewModel 本质上是数据，Model 与 ViewModel 的界限非常模糊，很可能导致过于复杂的 ViewModel 逻辑。

- **我想要参与!**

  欢迎! 请阅读 [贡献指南](https://github.com/yyx990803/vue/blob/master/CONTRIBUTING.md)，或是加入 <a href="https://gitter.im/yyx990803/vue" target="_blank"><img src="https://badges.gitter.im/Join%20Chat.svg"></a> 参与讨论。

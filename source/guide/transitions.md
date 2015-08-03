title: 过渡效果
type: 教程
order: 12
---

通过 Vue.js 的过渡系统，你可以轻松的为 DOM 节点被插入/移除的过程添加过渡动画效果。Vue 将会在适当的时机添加/移除 CSS 类名来触发 CSS3 过渡/动画效果，你也可以提供相应的 JavaScript 钩子函数在过渡过程中执行自定义的 DOM 操作。

以 `v-transition="my-transition"` 这个指令为例，当带有这个指令的 DOM 节点被插入或移除时，Vue 将会：

1. 用 `my-transition` 这个 ID 去查找是否有注册过的 JavaScript 钩子对象。这个对象可以是由 `Vue.transition(id, hooks)` 全局注册，或是通过 `transitions` 选项定义在当前的组件内部。如果找到此对象，则会在过渡动画不同的阶段调用相应的钩子。

2. 自动探测目标元素是否应用了 CSS 过渡效果或者动画效果， 并在适当的时机添加/移除 CSS 类名。

3. 如果没有提供 JavaScript 钩子函数，也没有检测到相应的 CSS 过渡/动画效果， DOM的插入/移除会在下一帧立即执行。

<p class="tip">所有的 Vue.js 过渡效果只有在该 DOM 操作是通过 Vue.js 触发时才会生效。触发的方式可以是通过内置指令，比如 `v-if`，或是通过 Vue 实例的方法，比如 `vm.$appendTo()`。</p>

## CSS 过渡效果

一个典型的 CSS 过渡效果定义如下：

``` html
<div v-if="show" v-transition="expand">hello</div>
```

你还需要定义 `.expand-transition`, `.expand-enter` 和 `.expand-leave` 三个 CSS 类:

``` css
.expand-transition {
  transition: all .3s ease;
  height: 30px;
  padding: 10px;
  background-color: #eee;
  overflow: hidden;
}
.expand-enter, .expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;
}
```

同时，你也可以提供 JavaScript 钩子:

``` js
Vue.transition('expand', {

  beforeEnter: function (el) {
    el.textContent = 'beforeEnter'
  },
  enter: function (el) {
    el.textContent = 'enter'
  },
  afterEnter: function (el) {
    el.textContent = 'afterEnter'
  },
  enterCancelled: function (el) {
    // handle cancellation
  },

  beforeLeave: function (el) {
    el.textContent = 'beforeLeave'
  },
  leave: function (el) {
    el.textContent = 'leave'
  },
  afterLeave: function (el) {
    el.textContent = 'afterLeave'
  },
  leaveCancelled: function (el) {
    // handle cancellation
  }
})
```

<div id="demo"><div v-if="show" v-transition="expand">hello</div><button v-on="click: show = !show">Toggle</button></div>

<style>
.expand-transition {
  transition: all .3s ease;
  height: 30px;
  padding: 10px;
  background-color: #eee;
  overflow: hidden;
}
.expand-enter, .expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;
}
</style>

<script>
new Vue({
  el: '#demo',
  data: {
    show: true,
    transitionState: 'Idle'
  },
  transitions: {
    expand: {
      beforeEnter: function (el) {
        el.textContent = 'beforeEnter'
      },
      enter: function (el) {
        el.textContent = 'enter'
      },
      afterEnter: function (el) {
        el.textContent = 'afterEnter'
      },
      beforeLeave: function (el) {
        el.textContent = 'beforeLeave'
      },
      leave: function (el) {
        el.textContent = 'leave'
      },
      afterLeave: function (el) {
        el.textContent = 'afterLeave'
      }
    }
  }
})
</script>

这里使用的 CSS 类名由 `v-transition` 指令的值所决定。以 `v-transition="fade"` 为例，CSS 类 `.fade-transition` 将会一直存在，而 `.fade-enter` 和 `.fade-leave` 将会在合适的时机自动被添加或移除。当 `v-transition` 指令没有提供值的时候，所使用的 CSS 类名将会是默认的 `.v-transition`, `.v-enter` 和 `.v-leave`。

当 `show` 属性变化时，Vue 会依据其当前的值来插入/移除 `<div>` 元素，并在合适的时机添加/移除对应的 CSS 类，具体如下：

- 当 `show` 变为 false 时，Vue 将会：
  1. 调用 `beforeLeave` 钩子;
  2. 在元素上应用 CSS 类 `.v-leave` 来触发过渡效果;
  3. 调用 `leave` 钩子;
  4. 等待过渡效果执行完毕； (监听 `transitionend` 事件)
  5. 从 DOM 中移除元素并且移除 CSS 类 `.v-leave`。
  6. 调用 `afterLeave` 钩子。

- 当 `show` 为 true 时，Vue 将会：
  1. 调用 `beforeEnter` 钩子;
  2. 在元素上应用 CSS 类 `.v-enter`;
  3. 将元素插入DOM;
  4. 调用 `enter` 钩子;
  5. 应用 `.v-enter` 类, 然后强制 CSS 布局以保证 `.v-enter` 生效；最后移除 `.v-enter` 来触发元素过渡到原本的状态。
  6. 等待过渡效果执行完毕;
  7. 调用 `afterEnter` 钩子.

此外，如果一个正在执行进入的过渡效果的元素在过渡还未完成之前就被移除，则 `enterCancelled` 钩子将会被执行。这个钩子可以用于清理工作，比如移除在 `enter` 时创建的计时器。对于正在离开过渡中又被重新插入的元素同理。

上述所有的钩子函数执行时，其 `this` 都指向相应的 Vue 实例。如果一个元素本身是一个 Vue 实例的根节点，则此实例将被应用为 `this`；否则 `this` 指向该过渡指令所属的实例。

最后，`enter` 与 `leave` 钩子函数可以接受可选的第二个参数：一个回调函数。当你的函数签名中含有第二个参数时，即表示你期望使用此回调来显式地完成整个过渡过程，而不是依赖 Vue 去自动检测 CSS 过渡的 `transitionend` 事件。比如：

``` js
enter: function (el) {
  // 无第二个参数
  // 过渡效果的结束由 CSS 过渡结束事件来决定
}
```

vs.

``` js
enter: function (el, done) {
  // 有第二个参数
  // 过渡效果结束必须由手动调用 `done` 来决定
}
```

<p class="tip">当多个元素同时执行过渡效果时，Vue.js 会进行批量处理以保证只触发一次强制布局。</p>

## CSS 动画

CSS 动画通过与 CSS 过渡效果一样的方式进行调用，区别就是动画中 `.v-enter` 类并不会在节点插入 DOM 后马上移除，而是在 `animationend` 事件触发时移除。

**示例：** (省略了兼容性前缀)

``` html
<span v-show="show" v-transition="bounce">Look at me!</span>
```

``` css
.bounce-enter {
  animation: bounce-in .5s;
}
.bounce-leave {
  animation: bounce-out .5s;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
@keyframes bounce-out {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(0);
  }
}
```

<div id="anim" class="demo"><span v-show="show" v-transition="bounce">Look at me!</span><br><button v-on="click: show = !show">Toggle</button></div>

<style>
  .bounce-enter {
    -webkit-animation: bounce-in .5s;
    animation: bounce-in .5s;
  }
  .bounce-leave {
    -webkit-animation: bounce-out .5s;
    animation: bounce-out .5s;
  }
  @keyframes bounce-in {
    0% {
      transform: scale(0);
      -webkit-transform: scale(0);
    }
    50% {
      transform: scale(1.5);
      -webkit-transform: scale(1.5);
    }
    100% {
      transform: scale(1);
      -webkit-transform: scale(1);
    }
  }
  @keyframes bounce-out {
    0% {
      transform: scale(1);
      -webkit-transform: scale(1);
    }
    50% {
      transform: scale(1.5);
      -webkit-transform: scale(1.5);
    }
    100% {
      transform: scale(0);
      -webkit-transform: scale(0);
    }
  }
  @-webkit-keyframes bounce-in {
    0% {
      -webkit-transform: scale(0);
    }
    50% {
      -webkit-transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(1);
    }
  }
  @-webkit-keyframes bounce-out {
    0% {
      -webkit-transform: scale(1);
    }
    50% {
      -webkit-transform: scale(1.5);
    }
    100% {
      -webkit-transform: scale(0);
    }
  }
</style>

<script>
new Vue({
  el: '#anim',
  data: { show: true }
})
</script>

## 纯 JavaScript 过渡效果

你也可以只使用 JavaScript 钩子，不定义任何 CSS 过渡规则。当只使用 JavaScript 钩子时，`enter` 和 `leave` 钩子必须使用 `done` 回调，否则它们将会被同步调用，过渡将立即结束。下面的示例中我们使用 jQuery 来注册一个自定义的 JavaScript 过渡效果：

``` js
Vue.transition('fade', {
  enter: function (el, done) {
    // 此时元素已被插入 DOM
    // 动画完成时调用 done 回调
    $(el)
      .css('opacity', 0)
      .animate({ opacity: 1 }, 1000, done)
  },
  enterCancelled: function (el) {
    $(el).stop()
  },
  leave: function (el, done) {
    // 与 enter 钩子同理
    $(el).animate({ opacity: 0 }, 1000, done)
  },
  leaveCancelled: function (el) {
    $(el).stop()
  }
})
```

定义此过渡之后，你就可以通过给 `v-transition` 指定对应的 ID 来调用它：

``` html
<p v-transition="fade"></p>
```

<p class="tip">如果一个只使用 JavaScript 过渡效果的元素恰巧也受到其它 CSS 过渡/动画规则的影响，这可能会对 Vue 的 CSS 过渡检测机制产生干扰。碰到这样的状况时，你可以通过给你的钩子对象添加 `css: false` 来禁止 CSS 检测。</p>

## 渐进过渡效果

当同时使用 `v-transition` 和 `v-repeat` 时，我们可以为列表元素添加渐进的过渡效果，你只需要为你的过渡元素加上 `stagger`, `enter-stagger` 或者 `leave-stagger` 特性（以毫秒为单位）：

``` html
<div v-repeat="list" v-transition stagger="100"></div>
```

或者你也可以提供 `stagger`, `enterStagger` 或 `leaveStagger` 钩子来进行更细粒度的控制：

``` js
Vue.transition('stagger', {
  stagger: function (index) {
    // 为每个过渡元素增加 50ms 的延迟,
    // 但是最大延迟为 300ms
    return Math.min(300, index * 50)
  }
})
```

示例：

<iframe width="100%" height="200" style="margin-left:10px" src="http://jsfiddle.net/yyx990803/ujqrsu6w/embedded/result,html,js,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

下一节：[创建大型应用](../guide/application.html).

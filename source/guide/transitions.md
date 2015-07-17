title: 过渡效果
type: guide
order: 12
---

通过Vue.js的过渡系统，在节点插入/移除DOM的过程中，你可以轻松的运用自动过渡效果。Vue.js将会在适当的时机添加/移除CSS classes来触发CSS过渡/动画效果，你也提供相应的JavaScript钩子函数在动画过渡期间执行自定义的DOM操作。

通过使用`v-transition="my-transition"`指令的运用，Vue将会：

1. 试着找到一个注册过的JavaScript钩子对象，无论是通过`Vue.transition(id, hooks)`，`transitions` 选项，或者使用id `"my-transition"`。 如果找到此对象，则在过渡动画不同的阶段调用相应的钩子

2. 自动探测目标元素是否应用了CSS 过渡效果或者动画效果， 并在适当的时机添加/移除CSS classes

3. 如果没有提供JavaScript钩子函数或者检测到相应的CSS 过渡/动画效果， DOM的操作（插入/移除）则会在下一帧立即执行

<p class="tip">所有的Vue.js过渡效果只会在通过Vue.js应用的DOM操作，或者通过内建指令，比如`v-if`，或者通过Vue的实例方法，比如`vm.$appendTo()`触发</p>

## CSS 过渡效果

一个典型的CSS过渡效果定义如下：

``` html
<div v-if="show" v-transition="expand">hello</div>
```

你还需要定义CSS规则给 `.expand-transition`, `.expand-enter` 和 `.expand-leave` 三个classes:

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

此外，你也可以提供JavaScript钩子:

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

这些class根据`v-transition`指令指定的值进行添加和切换。在指定`v-transition="fade"`这个例子中，CSS类`.fade-transition` 将会一直存在，CSS类`.fade-enter` 和 `.fade-leave`将会在正确的时机自动切换。当没有提供这些值的时候，将会切换会默认的`.v-transition`, `.v-enter` 和 `.v-leave`。

当`show`属性发生变化，Vue.js依据其变化来插入/移除`<p>`元素，并使用过渡class，具体如下：

- 当`show`为false时，Vue.js将会：
  1. 调用 `beforeLeave` 钩子;
  2. 在元素上应用CSS类 `v-leave`来触发过渡效果
  3. 调用 `leave` 钩子;
  4. 等待过渡效果执行完毕； (监听 `transitionend` 事件)
  5. 从DOM中移除元素并且移除CSS类 `v-leave`。
  6. 调用 `afterLeave` 钩子。

- 当`show`为true时，Vue.js将会：
  1. 调用 `beforeEnter` 钩子;
  2. 将 `v-enter` 应用到元素上;
  3. 将元素插入DOM;
  4. 调用 `enter` 钩子;
  5. 强制CSS布局，`v-enter` 将会自动被应用, 然后移除CSS类 `v-enter` 来触发元素回归到原本的状态。
  6. 等待过渡效果执行完毕;
  7. 调用 `afterEnter` 钩子.

此外，如果删除一个正在执行进入的过渡效果的元素时，`enterCancelled` 钩子将会被执行，可以用于清除变化或者在`enter`时创建的计时器，当执行离开过渡掉过的时候也可以如此操作。

上述所有的猴子函数执行时，其`this`指向相应的Vue实例。如果元素本身为Vue实例的根节点，则此实例被应用为当前上下文，否则，上下文环境则为过渡指令所属的实例

最后`enter` 与 `leave`可以设置第二个参数设置回调。这样做即表示当你调用此回调来替代原有的`transitionend` 事件，此时Vue.js会期望通过执行此回调来表示过渡效果执行完毕。比如：

``` js
enter: function (el) {
  // 无第二个参数
  // 过渡效果结束是通过CSS 过渡结束事件来确定
}
```

vs.

``` js
enter: function (el, done) {
  // 有第二个参数
  // 过渡效果结束是当`done` 被调用
}
```
<p class="tip">当多个元素同时执行过渡效果是，Vue.js会批量处理他们并只会触发一次强制布局</p>

## CSS 动画

CSS 动画通过与CSS过渡效果一样的方式进行调用，区别就是动画中`v-enter`并不会在节点插入DOM后马上移除，而是在`animationend`回调时移除

**示例：** (省略了css前缀的规则)

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

## 只应用JavaScript的过渡效果

你也可以只使用JavaScript钩子，不定义任何CSS规则。当只使用JavaScript的过渡效果，`enter`和`leave`钩子需要`done`回调，否则他将会被同步调用，过渡将立即结束。下面的示例中使用jQuery来注册一个自定义JavaScript的过渡效果：

``` js
Vue.transition('fade', {
  enter: function (el, done) {
    // element is already inserted into the DOM
    // call done when animation finishes.
    $(el)
      .css('opacity', 0)
      .animate({ opacity: 1 }, 1000, done)
  },
  enterCancelled: function (el) {
    $(el).stop()
  },
  leave: function (el, done) {
    // same as enter
    $(el).animate({ opacity: 0 }, 1000, done)
  },
  leaveCancelled: function (el) {
    $(el).stop()
  }
})
```

之后你就可以通过给`v-transition`指定过渡ID来应用,比如：

``` html
<p v-transition="fade"></p>
```

<p class="tip">如果一个只使用JavaScript定义过渡效果的元素恰巧拥有其他的过渡效果或者动画效果被应用时，可能会与Vue的过渡定义冲突。碰到这样的状况时，你可以通过给你的过渡对象增加`css: false`来禁止Vue监听CSS相关的过渡效果</p>

## 惊人的过渡效果

当时同时使用`v-transition` 和 `v-repeat`时这种惊人的过渡效果是可能产生的。你可以通过增加一个`stagger`, `enter-stagger` 或者 `leave-stagger`属性应用于你的元素：

``` html
<div v-repeat="list" v-transition stagger="100"></div>
```

或者你也可以提供`stagger`, `enterStagger` 或 `leaveStagger` 钩子来进行细粒度的控制

``` js
Vue.transition('stagger', {
  stagger: function (index) {
    // 为每个过渡项增加50ms的延迟,
    // 但是最大延迟为300ms
    return Math.min(300, index * 50)
  }
})
```

例如：

<iframe width="100%" height="200" style="margin-left:10px" src="http://jsfiddle.net/yyx990803/ujqrsu6w/embedded/result,html,js,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

下一节：[创建大型应用](../guide/application.html).
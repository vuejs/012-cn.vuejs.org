title: 安装
type: 教程
order: 1
vue_version: 0.12.15
dev_size: "234.98"
min_size: "71.09"
gz_size: "23.15"
---

> **兼容性提示：**Vue.js 不支持 IE8 及其以下版本。

## 独立版本

直接下载并用 `<script>` 标签引入，`Vue` 就会被注册为一个全局变量。

<div id="downloads">
<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.js" download>开发版本</a><span class="light info">{{dev_size}}kb, 包含丰富的注释和警告信息。</span>

<a class="button" href="https://raw.github.com/yyx990803/vue/{{vue_version}}/dist/vue.min.js" download>生产版本</a><span class="light info">{{min_size}}kb minified / {{gz_size}}kb gzipped</span>
</div>

### CDN

也可以在 [jsdelivr](//cdn.jsdelivr.net/vue/{{vue_version}}/vue.min.js) 或 [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.min.js) 获取 (版本更新可能会略滞后)。

### CSP 兼容版本

部分环境，诸如 Google Chrome Apps，强制要求内容安全策略 (CSP) 并且不允许使用 `new Function()` 来进行表达式求值。在此情况下，你可以用 [CSP 兼容版本](https://github.com/yyx990803/vue/tree/csp/dist)代替。

## NPM

``` bash
$ npm install vue
# 获取CSP兼容版本：
$ npm install vue@csp
# 获取最新开发版本(来自于GitHub):
$ npm install yyx990803/vue#dev
```

## Bower

``` bash
# Bower 只能够获得稳定版本
$ bower install vue
```

## AMD 模块加载器

直接下载或通过 Bower 安装的版本已经用 UMD 接口包装过，可以直接作为 AMD 模块使用。

## 准备好了吗？

[走起！](../guide/)

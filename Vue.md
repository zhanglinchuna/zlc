### 一、Vue安装

#### 1.1 直接使用 <script> 标签引入

```
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

### 二、Vue的常用指令

- v-cloak

不使用`v-cloak`的时候

```html
<div id="app">
    +++{{msg}}+++
</div>
```
```javascript
<script>
var app = new Vue({
    el: '#app',
    data: {
        msg:'hello'
    }
});
</script>
```
> 问题：当网络较慢，网页还在加载 Vue.js ，而导致 Vue 来不及渲染，这时页面就会显示出 Vue 源代码 {{msg}}

使用`v-cloak`可以解决网络慢环境下页面中表达式显示问题

```html
<div id="app" v-cloak>
    +++{{msg}}+++
</div>
```
页面显示：

```
+++hello+++
```

- v-text

`v-text` 默认是不会在页面中显示表达式问题的，但是`v-text`会覆盖元素中原本的内容

```html
<div id="app">
    <h4 v-text="msg">+++你好+++</h4>
</div>
```
页面显示：

```
hello
```
> v-cloak 和 v-text 区别：v-text会覆盖元素中原本的内容，v-cloak 不会覆盖元素中原本的内容

- v-html

`v-html` 是会将html标签转义后输出，`v-text` 将标签原样输出，`v-html` 也是会覆盖元素中原本的内容

```html
<div id="app">
    +++{{msg2}}+++
</div>
```
```javascript
<script>
var app = new Vue({
    el: '#app',
    data: {
        msg:'hello',
        msg2:'<h1>h1的 hello</h1>'
    }
});
</script>
```

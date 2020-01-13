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
    {{context}}
</div>
```
```javascript
<script>
var app = new Vue({
    el: '#app',
    data: {
        context:'hello'
    }
});
</script>
```
> 问题：当网络较慢，网页还在加载 Vue.js ，而导致 Vue 来不及渲染，这时页面就会显示出 Vue 源代码{{context}}

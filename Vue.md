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
<div id="app">
    <h4 v-cloak="msg">+++{{msg}}+++</h4>
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
    <h4 v-html="msg2">+++你好+++</h4>
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

- v-bind 属性绑定指令

```html
<div id="app">
    <!--给input标签绑定一个value属性-->
    <input type="button" v-bind:value="value">
    <!--v-bind指令可以简写 : -->
    <!--<input type="button" :value="value">-->
</div>
```
```javascript
<script>
var app = new Vue({
    el: '#app',
    data: {
        msg:'hello',
        msg2:'<h1>h1的 hello</h1>',
        value:'按钮'
    }
});
</script>
```

- v-on 事件绑定指令

```html
<div id="app">
    <!--给input标签绑定一个点击事件-->
    <input type="button" value="按钮" v-on:click=="show('test')">
    <!--v-on指令可以简写 @ -->
    <!--<input type="button" value="按钮" @click="show('test')">-->
</div>
```
```javascript
<script>
var app = new Vue({
    el: '#app',
    data: {
        msg:'hello',
        msg2:'<h1>h1的 hello</h1>',
        value:'按钮'
    },
    methods: { // 这个 methods属性中定义了当前Vue实例所有可用的方法
        show: function (args) {
          alert(this.msg + args)
        }
    }
});
</script>
```

- v-model 双向数据绑定

`v-bind` 只能实现数据的单向绑定，修改Vue实例data属性值改变页面中value的值，而无法实现数据的双向绑定，修改页面中value的值Vue实例data属性值不会改变

`v-model` 指令，可以实现页面表单元素和 Model 中数据的双向数据绑定

```html
<div id="app">
    <h4>{{msg}}</h4>
    <input type="text" v-model="msg">
</div>
```
```javascript
<script>
var app = new Vue({
    el: '#app',
    data: {
       msg:'hello vue...'
    }
});
</script>
```
> 注意：v-model 只能运用在表单元素中input、button、select、textarea...

### 三、事件修饰符

- .prevent 阻止默认行为

.prevent等同于JavaScript的event.preventDefault()，用于取消默认事件。比如我们页面的<a href="#">标签，当用户点击时，通常在浏览器的网址列出#

```html
<div id="app">
    <!--点击下面a链接是并不会跳转到href地址，只是调用了点击事件linkClick方法-->
    <a href="http://www.baidu.com" @click.prevent="linkClick">百度一下你就知道</a>
</div>
```
```javascript
var app = new Vue({
    el: '#app',
    methods: {
       linkClick() {
                console.log('触发了连接的点击事件')
            }
    }
});
```

- .capture 捕获触发事件

嵌套两三层父子关系的标签，当我们先点击子节点，就会先触发最外层 父节点 的事件，然后在执行子节点的事件，父节点--> 子节点

```html
<div id="app">
    <!--我们点击button按钮，先会执行divHandler方法，然后执行btnHandler方法-->
    <div class="inner" @click.capture="divHandler">
      <input type="button" value="按钮" @click="btnHandler">
    </div>
</div>
```
```javascript
var app = new Vue({
    el: '#app',
    methods: {
       divHandler() {
                console.log('这是触发了 inner div 的点击事件')
            },
        btnHandler() {
            console.log('这是触发了 btn 按钮 的点击事件')
        }
    }
});
```
控制台打印：

```
这是触发了 inner div 的点击事件
这是触发了 btn 按钮 的点击事件
```

- .self 只触发自己范围内的事件

```html
<div id="app">
    <!--我们点击button按钮，并不会触发外层div的点击事件，只会执行自己的事件方法-->
    <div class="inner" @click.capture="divHandler">
      <input type="button" value="按钮" @click="btnHandler">
    </div>
</div>
```
```javascript
var app = new Vue({
    el: '#app',
    methods: {
       divHandler() {
                console.log('这是触发了 inner div 的点击事件')
            },
        btnHandler() {
            console.log('这是触发了 btn 按钮 的点击事件')
        }
    }
});
```
控制台打印：

```
这是触发了 btn 按钮 的点击事件
```

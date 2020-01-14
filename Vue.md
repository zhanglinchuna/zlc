### 一、Vue安装

#### 1.1 直接使用 <script> 标签引入

```
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

### 二、Vue的样式

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <style>
        .red {
            color: red; /*红色字体*/
        }

        .thin {
            font-weight: 200; /*字体宽度*/
        }

        .italic {
            font-style: italic; /*字体倾斜*/
        }

        .active {
            letter-spacing: 0.5em; /*字体间隔*/
        }
    </style>
</head>

<body>
<div id="app">

    <!--  普通的css样式 -->
    <h1 class="red thin">普通的给类属性添加样式</h1>

    <!-- 使用Vue的方式，直接传递一个数组，注意： 这里的 class 需要使用  v-bind 做数据绑定 -->
    <h1 :class="['thin', 'italic']">使用Vue的方式，绑定class属性添加样式</h1>

    <!-- 在数组中使用三元表达式 ，当flag为true时active样式生效-->
    <h1 :class="['thin', 'italic', flag?'active':'']">使用Vue的方式，在class属性中使用三元表达式添加属性</h1>

    <!-- 在数组中使用 对象来代替三元表达式，当flag为true时active样式生效，提高代码的可用性 -->
    <h1 :class="['thin', 'italic', {'active':flag} ]">使用Vue的方式，在class属性中添加对象，提高代码的可用性</h1>

    <!-- 在为 class 使用 v-bind 绑定 对象的时候，对象的属性是类名，由于 对象的属性可带引号，也可不带引号，所以 这里我没写引号；属性的值 是一个标识符 -->
    <h1 :class="classObj">使用Vue的方式，在class属性中绑定一个对象</h1>

    <!-- 使用Vue的方式，添加行内样式 -->
    <h1 :style="styleObj1">Vue中的style样式1</h1>

    <h1 :style="[ styleObj1, styleObj2 ]">Vue中的style样式2</h1>
</div>

<script>
    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
        el: '#app',
        data: {
            flag: true,
            classObj: {red: true, thin: true, italic: false, active: false},
            styleObj1: {color: 'red', 'font-weight': 200},
            styleObj2: {'font-style': 'italic'}
        }
    });
</script>
</body>

</html>
```

### 三、事件修饰符

- .prevent 阻止默认行为

.prevent等同于JavaScript的event.preventDefault()，用于取消默认事件。比如我们页面的&lt;a href="#"&gt;标签，当用户点击时，通常在浏览器的网址列出#

```html
<div id="app">
    <!--点击下面a链接是并不会跳转到href地址，只是调用了点击事件linkClick方法-->
    <a href="http://www.baidu.com" @click.prevent="linkClick">百度一下你就知道</a>
</div>
```
```javascript
var vm = new Vue({
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
var vm = new Vue({
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

- .stop 防止事件冒泡

冒泡事件：嵌套两三层父子关系的标签，当我们先点击子节点，就会先触发内层的子节点的事件，然后在执行外层的父节点事件，子节点--> 父节点

```html
<div id="app">
    <!--我们点击button按钮，执行btnHandler方法，并不会触发外层div的事件-->
    <div class="inner" @click="divHandler">
      <input type="button" value="按钮" @click.stop="btnHandler">
    </div>
</div>
```
```javascript
var vm = new Vue({
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

- .self 只阻止自己范围内的事件

```html
<!--点击button按钮-->
<div class="outer" @click="divHandler_outer">
  <div class="inner" @click.self="divHandler_inner">
    <input type="button" value="按钮" @click="btnHandler">
  </div>
</div>
```
```javascript
var vm = new Vue({
    el: '#app',
    methods: {
       divHandler_inner() {
            console.log('这是触发了 inner div 的点击事件')
        },
        divHandler_outer() {
            console.log('这是触发了 outer div 的点击事件')
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
这是触发了 outer div 的点击事件
```

- .once 只触发一次事件处理函数

如果我们在@click事件上添加.once修饰符，只要点击按钮只会执行一次linkClick方法，第二次点击直接跳转到a连接的href地址上

```html
<div id="app">
    <!--第一次点击下面a链接时会调用linkClick方法，第二次点击直接跳转到a连接的href地址上-->
    <a href="http://www.baidu.com" @click.prevent.once="linkClick">有问题，先去百度</a>
</div>
```
```javascript
var vm = new Vue({
    el: '#app',
    methods: {
       linkClick() {
            console.log('触发了连接的点击事件')
        }
    }
});
```

### 四、键盘修饰符

#### 4.1 通过Vue.config.keyCodes.名称 = 按键值 来自定义案件修饰符的别名：

```
Vue.config.keyCodes.enter = 13;
```

#### 4.2 使用自定义的按键修饰符

```
<input type="text" @keyup.enter="add()">
```
enter键抬起事件

```html
<div id="app">
    <input type="text" @keyup.enter="add()"><br>
</div>
<script>
    Vue.config.keyCodes.enter = 13;
    var vm = new Vue({
        el: "#app",
        methods: {
            add: function () {
                console.log("enter键抬起事件")
            }
        }
    });
</script>
```

### 五、Vue的常用指令

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

- v-if 条件渲染

```html
<!--当flag的为true时显示h3标签内容，为false不展示-->
<h3 v-if="flag">这是用v-if控制的元素</h3>
```
- v-show 条件渲染

```html
<!--当flag的为true时显示h3标签内容，为false不展示-->
<h3 v-show="flag">这是用v-show控制的元素</h3>
```
> v-if 的特点：每次都会重新删除或创建元素<br>
> v-show 的特点： 每次不会重新进行DOM的删除和创建操作，只是切换了元素的 display:none 样式

- v-for 数据遍历

遍历数组

```html
<div id="app">
    <p v-for="(item, i) in list">索引值：{{i}} --- 每一项：{{item}}</p>
</div>
```
```javascript
var vm = new Vue({
    el: '#app',
    data: {
    list: [1, 2, 3]
    }
});
```
```
索引值：0 --- 每一项：1
索引值：1 --- 每一项：2
索引值：2 --- 每一项：3
```
遍历对象数组

```html
<div id="app">
    <p v-for="(user, i) in list" :key="user.id>Id：{{ user.id }} --- 名字：{{ user.name }} --- 索引：{{i}}</p>
</div>
```
```javascript
var vm = new Vue({
    el: '#app',
    data: {
        list: [
              { id: 1, name: 'zs1' },
              { id: 2, name: 'zs2' },
              { id: 3, name: 'zs3' }
            ]
    }
});
```
```
Id：1 --- 名字：zs1 --- 索引：0
Id：2 --- 名字：zs2 --- 索引：1
Id：3 --- 名字：zs3 --- 索引：2
```

遍历对象

```html
<div id="app">
    <!-- 注意：在遍历对象身上的键值对的时候， 除了 有  val  key  ,在第三个位置还有 一个 索引  -->
    <p v-for="(val, key, i) in user">值是： {{ val }} --- 键是： {{key}} --- 索引： {{i}}</p>
</div>
```
```javascript
var vm = new Vue({
    el: '#app',
    data: {
        user: {
          id: 1,
          name: '张三',
          gender: '男'
        }
    }
});
```
```
值是： 1 --- 键是： id --- 索引： 0
值是： 张三 --- 键是： name --- 索引： 1
值是： 男 --- 键是： gender --- 索引： 2
```

遍历数字

```html
<div id="app">
    <!-- in 后面我们放过  普通数组，对象数组，对象， 还可以放数字 -->
    <!-- 注意：如果使用 v-for 迭代数字的话，前面的 count 值从 1 开始 -->
    <p v-for="count in 5">这是第 {{ count }} 次循环</p>
</div>
```
```
这是第 1 次循环
这是第 2 次循环
这是第 3 次循环
这是第 4 次循环
这是第 5 次循环
```

v-for 循环的时候，使用 key 属性指定 唯一的 字符串/数字 类型

```html
<div id="app">
    <!-- 注意： v-for 循环的时候，key 属性只能使用 number获取string -->
    <!-- 注意： key 在使用的时候，必须使用 v-bind 属性绑定的形式，指定 key 的值 -->
    <!-- 在组件中，使用v-for循环的时候，或者在一些特殊情况中，如果 v-for 有问题，必须 在使用 v-for 的同时，指定 唯一的 字符串/数字 类型 :key 值 -->
    <p v-for="(user, i) in list" :key="user.id>Id：{{ user.id }} --- 名字：{{ user.name }} --- 索引：{{i}}</p>
</div>
```
```javascript
var vm = new Vue({
    el: '#app',
    data: {
        list: [
              { id: 1, name: 'zs1' },
              { id: 2, name: 'zs2' },
              { id: 3, name: 'zs3' }
            ]
    }
});
```

### 五、自定义指令

#### 5.1 自定义全局指令

```html
<div id="app">
    <p>页面载入时，input 元素自动获取焦点：</p>
    <input v-focus>
</div>
 
<script>
// 注册一个全局自定义指令 v-focus
Vue.directive('focus', {
  // inserted 表示被绑定元素插入父节点时调用
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
// 创建根实例
new Vue({
  el: '#app'
})
</script>
```

#### 5.1 自定义局部指令

```html
<div id="app">
    <p v-color="'red'">自定义指令为字体设置颜色</p><br>
    <p v-font-weight="800">自定义指令为字体设置宽度</p>
</div>
 
<script>
// 创建根实例
new Vue({
  el: '#app',
  directives: {
        color: { // 为元素设置指定的字体颜色
            bind(el, binding) {
                el.style.color = binding.value;
            }
        },
        'font-weight': function (el, binding2) { // 自定义指令的简写形式，等同于定义了 bind 和 update 两个钩子函数
            el.style.fontWeight = binding2.value;
        }
    }
})
</script>
```

### 创建组件的三种方式

#### 创建组件的方式1

使用 Vue.extend 方法来创建全局的Vue组件

```html
<div id="app">
    <!-- 3. 如果使用 Vue.component 定义全局组件的时候，组件名称使用了 驼峰命名，则在引用组件的时候，需要把 大写的驼峰改为小写的字母，同时，两个单词之前，使用 - 连接 -->
    <my-com></my-com>
</div>
<script>
    //1. 使用 Vue.extend 来创建全局的Vue组件
    let com = Vue.extend({
        //1.1 通过 template 属性，指定了组件要展示的HTML结构，且HTML中必须只能有唯一的一个根元素
        template: '<h3>这是使用 Vue.extend 创建的组件</h3>'
    });
    //2. 使用 Vue.component('组件的名称', 创建出来的组件模板对象)注册模板对象
    Vue.component('myCom', com);
    // 如果不使用驼峰,则直接拿名称来使用即可;<mycom></mycom>
    // Vue.component('mycom', com)
    let vm = new Vue({
        el: "#app"
    });
</script>
```
#### 创建组件的方式2

直接使用 Vue.component 方法创建全局的Vue组件

```html
<div id="app">
    <mycom></mycom>
</div>
<script>
    Vue.component('mycom', {
        //注意:不论是哪种方式创建出来的组件,组件的 template 属性指向的模板内容,必须有且只能有唯一的一个根元素<div>
        template: '<div><h3>这是直接使用 Vue.component 创建出来的组件</h3><span>组件的 template 必须有且只能有唯一的一个根元素</span></div>'
    })
    let vm = new Vue({
        el: "#app"
    });
</script>
```

#### 创建组件的方式3

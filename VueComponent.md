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

使用 template 标签创建Vue组件

```html
<div id="app">
    <global-com></global-com>
    <my-com></my-com>
</div>
<!-- 在 VM 实例控制区域的外面,使用 template 元素,定义组件的HTML模板结构  -->
<template id="tem">
    <div><p>这是通过 template 标签，创建的组件</p></div>
</template>
<script>
    //定义全局组件
    Vue.component('globalCom', {
        template: "#tem"
    })

    let vm = new Vue({
        el: "#app",
        components: {   //只能在 #app 内使用的局部组件
            myCom: {
                template: "#tem"
            }
        }
    });
</script>
```

### Vue组件中的data和methods

1. 组件中的data必须是一个方法，且这个方法的返回值必须是一个对象。
2. 组件中的methods要想使用组件中的data数据，需要this.属性。

```html
<div id="app">
    <mycom></mycom><br>
    <mycom></mycom><br>
    <mycom></mycom>
</div>

<script>
    Vue.component('mycom', {
        template: "<div><h1>这是全局组件 --- {{msg}} --- {{count}}</h1><br><input type='button' value='点击' @click='increment()'/> </div>",
        data: function () {
            return {
                count: 0,
                msg: "这是组件的中data定义的数据"
            }
        },
        methods: {
            increment() {
                this.count++
            }
        }
    })

    let vm = new Vue({
        el: "#app",
    });
</script>
```

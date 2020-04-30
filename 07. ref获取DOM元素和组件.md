```html
<body>
<div id="app">

    <input type="button" value="获取元素" @click="getElement" ref="mybtn">
    <h3 id="myh3" ref="myh3">h3元素</h3>
    <hr>
    <login ref="mylogin"></login>

</div>

<script>
    var login = {
        template: '<h1>登录组件</h1>',
        data() {
            return {
                msg: 'son msg'
            }
        },
        methods: {
            show() {
                console.log('调用了子组件的方法')
            }
        }
    }

    // 创建 Vue 实例，得到 ViewModel
    var vm = new Vue({
        el: '#app',
        data: {},
        methods: {
            getElement() {
                // console.log(document.getElementById('myh3').innerText)
                //  ref  是 英文单词 【reference】   值类型 和 引用类型  referenceError
                console.log(this.$refs.myh3.innerText); // h3元素
                console.log(this.$refs.mylogin.msg); // son msg
                this.$refs.mylogin.show(); // 调用了子组件的方法
            }
        },
        components: {
            login
        }
    });
</script>
</body>
```

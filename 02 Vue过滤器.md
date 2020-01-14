### 私有过滤器

```html
<div id="app">
    <p>{{new Date() | dateFormat('YYYY-mm-dd HH:MM')}}</p>
</div>
<script>
var vm = new Vue({
    el: "#app",
    filters: {
        dateFormat(el, fmt) {
            // 获取年月日
            let y = el.getFullYear();
            let m = (el.getMonth() + 1).toString().padStart(2, '0');
            let d = el.getDate().toString().padStart(2, '0');
            // 获取时分秒
            let hh = el.getHours().toString().padStart(2, '0');
            let mm = el.getMinutes().toString().padStart(2, '0');
            let ss = el.getSeconds().toString().padStart(2, '0');

            if (fmt.toLowerCase() === 'YYYY-mm-dd HH:MM'.toLowerCase()) {
                return `${y}-${m}-${d} ${hh}:${mm}:${ss}`;
            } else {
                return `${y}-${m}-${d}`;
            }
        }
    }
})
</script>
```

### 全局过滤器

```html
<div id="app">
    <p>{{new Date() | dateFormat('YYYY-mm-dd HH:MM')}}</p>
</div>
<script>
Vue.filter('dateFormat', function (el, fmt) {
    // 获取年月日
    let y = el.getFullYear();
    let m = (el.getMonth() + 1).toString().padStart(2, '0');
    let d = el.getDate().toString().padStart(2, '0');
    // 获取时分秒
    let hh = el.getHours().toString().padStart(2, '0');
    let mm = el.getMinutes().toString().padStart(2, '0');
    let ss = el.getSeconds().toString().padStart(2, '0');

    if (fmt.toLowerCase() === 'YYYY-mm-dd HH:MM'.toLowerCase()) {
        return `${y}-${m}-${d} ${hh}:${mm}:${ss}`;
    } else {
        return `${y}-${m}-${d}`;
    }
})
var vm = new Vue({
    el: "#app"
})
</script>
```

## 1. hello world

```html
<!DOCTYPE html>
<html lang="Zh-CN">

<head>
    <meta charset="UTF-8" />
    <script src="../js/vue.js"></script>
</head>

<body>
    <div id="root">
        <h1>hello {{key}}</h1>
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false
        
        new Vue({
            el: '#root',
            data:{
                key:"val"
            }
        })
        
    </script>
</body>

</html>
```

1. 先导入`Vue.js`
2. 创建`Vue`对象，向里面加实例
3. `el`表示选择器，一个 Vue 只能和一个容器进行绑定
4. 在html里可以通过`{{}}`来获取变量
4. `{{}}`里面可以放js表达式





## 2. 模版语法

1. 插值语法：`{{}}`

2. 指令语法：`<a v-bind:href="url">跳转2</a>`
   会将引号里面的内容当做一个表达式去求值，如果没有，报错

   > v-bind：可以简写为：



插值语法用于标签体内容，指令语法用于标签属性


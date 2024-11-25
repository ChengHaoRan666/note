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



## 3. 数据绑定

`v-bind`：单向改变。可以将key的值赋值给val

`v-model`：双向改变。key的值和val的值相互改变

> 但是`v-model`的使用具有局限性，只能使用在表达类元素（输入类元素）上

`v-model:value`可以简写为`v-model`



## 4. EL 和 data的两种写法

`EL`还可以用`vue`的实例的`$mount`属性来代替

`el: '#root' ` 等同于：`v.$mount('#root')`



data可以用一个函数代替，函数的返回值就是要的结果

```javascript
			data: function () {
                return {
                    name: '值'
                }
            }

或
			data() {
                return {
                    name: '值'
                }
            }
```



```html
<!DOCTYPE html>
<html lang="Zh-CN">

<head>
    <meta charset="UTF-8" />
    <script src="../js/vue.js"></script>
</head>

<body>
    <div id="root">
        <h2>name:{{name}}</h2>


    </div>

    <script type="text/javascript">
        const v = new Vue({
            el: '#root', // EL第一种写法
            // data的第一种写法:对象式
            // data: {
            //     name: '值'
            // }


            // data的第二种写法:函数式
            data() {
                return {
                    name: '值'
                }
            }

        })
        // v.$mount('#root') // EL第二种写法
    </script>
</body>
</html>
```





## 5. 数据代理

### 1. Object.defineProperty方法

`Object.defineProperty`方法用于对对象添加新的属性

第一个参数是要为添加的对象

第二个参数是要添加的属性名

第三个参数是个配置项{}

```html
<!DOCTYPE html>
<html lang="Zh-CN">

<head>
    <meta charset="UTF-8" />
    <script src="../js/vue.js"></script>
    <title>回顾Object.defineproperty方法.html</title>
</head>

<body>
    <script type="text/javascript">
        let person = {
            name: '张三',
            sex: '男'
        }
        Object.defineProperty(person, 'age', {
            value: 18
        })

        console.log(person)
    </script>
</body>

</html>
```

通过`Object.defineProperty`添加的属性默认不参与**枚举**，如果想让他参与枚举，添加一个配置项`enumerable:true`

通过`Object.defineProperty`添加的属性默认**不可以修改**，如果想让他可以修改，添加一个配置项`writable:true`

通过`Object.defineProperty`添加的属性默认**不可以删除**，如果想让他可以修改，添加一个配置项`configurable:true`



`Object.defineProperty`还有很多高级配置：

```javascript
// get配置可以让age的值变为动态的
get: function () {
    return number;
}
```

```javascript
// set配置可以在修改值的时候为其他值赋值
set(value) {
    number = value;
}
```



总结：

```javascript
<script type="text/javascript">
        let number = 18;
        let person = {
            name: '张三',
            sex: '男'
        }
        Object.defineProperty(person, 'age', {
            // value: 18,
            // enumerable: true, // 控制属性是否可以枚举，默认false
            // writable: true, // 控制属性是否可以被修改，默认false
            // configurable: true, // 控制属性是否可以被删除，默认false

            // get配置可以让age的值变为动态的
            get: function () {
                return number;
            },

            // set配置可以在修改值的时候为其他值赋值
            set(value) {
                number = value;
            }
        })

        console.log(person)
        console.log(Object.keys(person))
    </script>
```



### 2. 什么是数据代理


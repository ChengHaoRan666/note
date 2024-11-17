## JSON

### json的定义：

json 是由键值对组成，并且由花括号（大括号）包围。每个键由引号引起来，键和值之间使用冒号进行分隔，多组键值对之间进行逗号进行分隔。

```javascript
var jsonObj = {
    "key1": 12,
    "key2": "abc",
    "key3": true,
    "key4": [11, "arr", false],
    "key5": {
        "key5_1": 551,
        "key5_2": "key5_2_value"
    },
    "key6": [{
        "key6_1_1": 6611,
        "key6_1_2": "key6_1_2_value"
    }, {
        "key6_2_1": 6621,
        "key6_2_2": "key6_2_2_value"
    }]
};
```



json的访问：

json 本身是一个对象。

json 中的 key 我们可以理解为是对象中的一个属性。

json 中的 key 访问就跟访问对象的属性一样： json 对象.key。

```javascript
alert(typeof(jsonObj));// object json 就是一个对象
alert(jsonObj.key1); //12
alert(jsonObj.key2); // abc
alert(jsonObj.key3); // true
alert(jsonObj.key4);// 得到数组[11,"arr",false]
// json 中 数组值的遍历
for(var i = 0; i < jsonObj.key4.length; i++) {
    alert(jsonObj.key4[i]);
}
alert(jsonObj.key5.key5_1);//551
alert(jsonObj.key5.key5_2);//key5_2_value
alert( jsonObj.key6 );// 得到 json 数组
// 取出来每一个元素都是 json 对象
var jsonItem = jsonObj.key6[0];
// alert( jsonItem.key6_1_1 ); //6611
alert( jsonItem.key6_1_2 ); //key6_1_2_value
```



### json的两个常用方法：

> json 的存在有两种形式。
>
> 一种是：对象的形式存在，我们叫它 json 对象。
>
> 一种是：字符串的形式存在，我们叫它 json 字符串。

> 一般我们要操作 json 中的数据的时候，需要 json 对象的格式。
>
> 一般我们要在客户端和服务器之间进行数据交换的时候，使用 json 字符串。

> JSON.stringify() 
>
> 把 json 对象转换成为 json 字符串

> JSON.parse() 
>
> 把 json 字符串转换成为 json 对象

```javascript
// 把 json 对象转换成为 json 字符串
var jsonObjString = JSON.stringify(jsonObj); // 特别像 Java 中对象的 toString
alert(jsonObjString)
// 把 json 字符串。转换成为 json 对象
var jsonObj2 = JSON.parse(jsonObjString);
alert(jsonObj2.key1);// 12
alert(jsonObj2.key2);// abc
```



### javaBean 和 json 的互转：

```java
/**
 * javaBean 和 json 的转换
 */
@Test
public void test1() {
    // 创建javaBean对象
    person person = new person("程", 18);
    // 创建gson示例
    Gson gson = new Gson();
    // 将javaBean转为json
    // toJson 可以将 javaBean 对象转为 json 字符串
    String json = gson.toJson(person);
    // 将json 字符串转为对应类型
    person fromPerson = gson.fromJson(json, person.class);
    System.out.println(json);
    System.out.println(fromPerson.toString());
}
```

### List 和 json 的互转：	

```java
/**
 * List 和 json 的转换
 */
@Test
public void test2() {
    List<person> personList = new ArrayList<>();
    personList.add(new person("a", 1));
    personList.add(new person("b", 2));
    personList.add(new person("c", 3));
    personList.add(new person("d", 4));
    personList.add(new person("e", 5));
    personList.add(new person("f", 5));
    Gson gson = new Gson();
    String json = gson.toJson(personList);
    List<person> lists = gson.fromJson(json, new TypeToken<List<person>>() {}.getType());
    System.out.println(json);
    System.out.println(lists);
    System.out.println(lists.get(0));
}
```

### Map 和 json 的互转：

```java
    /**
     * Map 和 json 的转换
     */
    @Test
    public void test3() {
        Map<Integer, Person> personMap = new HashMap<>();
        personMap.put(1, new Person("a", 1));
        personMap.put(2, new Person("b", 2));
        personMap.put(3, new Person("c", 3));

        Gson gson = new Gson();
        String json = gson.toJson(personMap);
        System.out.println(json);

        Map<Integer, Person> map = gson.fromJson(json, new TypeToken<Map<Integer, Person>>() {
        }.getType());
        System.out.println(map);
    }
}
```
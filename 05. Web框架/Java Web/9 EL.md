## EL 表达式

### 什么是 EL 表达式，EL表达式的作用：

EL 表达式的全称是：Expression Language。是表达式语言。

EL 表达式的什么作用：EL 表达式是代替 jsp 页面中的表达式脚本在 jsp 页面中进行数据的输出。

因为 EL 表达式在输出数据的时候，要比 jsp 的表达式脚本要简洁很多。

```jsp
<%
    request.setAttribute("key", "val");
%>
<%--表达式脚本输出--%>
<%=request.getAttribute("key")%><br>
<%--EL表达式输出--%>
${key}
```

> EL 表达式的格式是：${表达式}

> EL 表达式在输出 null 值的时候，输出的是空串。
>
> jsp 表达式脚本输出 null 值的时候，输出的是 null 字符串。



### EL 表达式搜索域数据的顺序：

```jsp
<%--向四个域对象中添加数据--%>
<%
    pageContext.setAttribute("key", "pageContext");
    request.setAttribute("key", "request");
    session.setAttribute("key", "session");
    application.setAttribute("key", "application");
%>

<%--通过表达式脚本中编写java代码获取对应值--%>
<%=pageContext.getAttribute("key")%><br>
<%=request.getAttribute("key")%><br>
<%=session.getAttribute("key")%><br>
<%=application.getAttribute("key")%><br>

<%--通过EL表达式获取对应值--%>
${key}
```



可以发现EL表达式获取的是PageContext 域中的值，多次实验可以得到搜索域数据顺序是：

> pageContext ==>>>  request  ==>>>  session  ==>>>  application





### EL 表达式输出复杂属性：

```jsp
<%--格式--%>
${想输出的值}
```

这个值是通过调用get方法得到的，所以得写上get方法。

```java
package com.chr.pojo;

import java.util.Arrays;
import java.util.List;
import java.util.Map;

/**
 * @Author: 程浩然
 * @Create: 2024/7/22 - 16:05
 * @Description: 用于测试EL表达式输出复杂属性所创建的类
 */
public class Person {
    private String name;
    private String[] phones;
    private List<String> cities;
    private Map<String, Object> map;
    private int age;

    
    public String toString() // 重写 toString 方法，内容省略
        
    public String getName() // getName 方法，内容省略
    public void setName(String name) // getName 方法，内容省略
    public String[] getPhones() //  getName 方法，内容省略
    public void setPhones(String[] phones) //  getName 方法，内容省略
    public List<String> getCities() //  getName 方法，内容省略
    public void setCities(List<String> cities) //  getName 方法，内容省略
    public Map<String, Object> getMap() //  getName 方法，内容省略
    public void setMap(Map<String, Object> map) //  getName 方法，内容省略
    public int getAge() //  getAge 方法，内容省略
    public void setAge(int age) //  setAge 方法，内容省略
}
```

```jsp
<body>
<%--先创建Person对象，为对象赋值，存入域中--%>
<%
    Person person = new Person();
    person.setName("张三");
    person.setPhones(new String[]{"181", "281"});
    person.setCities(new ArrayList<>(List.of(new String[]{"北京", "上海"})));
    HashMap<String, Object> map = new HashMap<>();
    map.put("key1", "val1");
    map.put("key2", "val2");
    map.put("key3", "val3");
    map.put("key4", "val4");
    person.setMap(map);
    person.setAge(18);

    pageContext.setAttribute("person", person);
%>

<%--EL 表达式输出--%>
${person}<br><br>
${person.name}<br>
<%--输出具体下标对应值--%>
${person.phones[1]}<br>
<%--输出地址值--%>
${person.cities[1]}<br>
${person.map}<br>
${person.age}<br>

</body>
```



### EL 表达式运算：

```jsp
<%--语法--%>
${ 运算表达式 }
```

##### 关系运算：

| 关系运算符 |   说明   |            范例            | 结果  |
| :--------: | :------: | :------------------------: | :---: |
|  == 或 eq  |   等于   |   `${5==5}`或`${5 eq 5}`   | true  |
|  != 或 ne  |  不等于  |  `${5 != 5}`或`${5 ne 5}`  | false |
|  < 或 lt   |   小于   |  `${3 < 5}`或`${3 lt 5}`   | true  |
|  > 或 gt   |   大于   | `${2 > 10}`或`${2 gt 10}`  | false |
|  <= 或 le  | 小于等于 | `${5 <= 12}`或`${5 le 12}` | true  |
|  >= 或 ge  | 大于等于 |  `${3 >= 5}`或`${3 ge 5}`  | false |



##### 逻辑运算：

| 逻辑运算符 |   说明   |                            范例                             | 结果  |
| :--------: | :------: | :---------------------------------------------------------: | :---: |
| && 或 and  |  与运算  | `${12 == 12 && 12 < 11}` 或 <br />`${12 == 12 and 12 < 11}` | false |
| \|\| 或 or |  或运算  | `${12 == 12 || 12 < 11}` 或 <br />`${12 == 12 or 12 < 11}`  | true  |
| ! 或  not  | 取反运算 |                 `${!true}` 或 `${not true}`                 | false |



##### 算数运算：

| 算数运算符 | 说明 |              范例               | 结果 |
| :--------: | :--: | :-----------------------------: | :--: |
|     +      | 加法 |          `${12 + 18}`           |  30  |
|     -      | 减法 |           `${18 - 8}`           |  10  |
|     *      | 乘法 |          `${12 * 12}`           | 144  |
|  / 或 div  | 除法 | `${144 / 12}`或`${144 div 12}`  |  12  |
|  % 或 mod  | 取模 | `${144 % 10}` 或`${144 mod 10}` |  4   |



##### empty运算：

empty 运算可以判断一个数据是否为空，如果为空，则输出 true,不为空输出 false。

以下几种情况为空：

1. 值为 null 值的时候，为空
2. 值为空串的时候，为空
3. 值是 Object 类型数组，长度为零的时候
4. list 集合，元素个数为零
5. map 集合，元素个数为零



##### 三元运算：

```jsp
<body>
${ 12 != 12 ? "a":"b" }
</body>
```



##### 点运算(.) 和 中括号运算([]):

.点运算，可以输出 Bean 对象中某个属性的值。

[]中括号运算，可以输出有序集合中某个元素的值。

并且[]中括号运算，还可以输出 map 集合中 key 里含有特殊字符的 key 的值。

```jsp
<body>
<%
    Map<String, Object> map = new HashMap<String, Object>();
    map.put("a", "aValue");
    map.put("a.a.a", "aaaValue");
    map.put("b+b+b", "bbbValue");
    map.put("c-c-c", "cccValue");
    request.setAttribute("map", map);
%>

${ map.a }<br>
${ map['a.a.a'] } <br>
${ map["b+b+b"] } <br>
${ map['c-c-c'] } <br>
</body>
```



### EL 表达式中 11 个隐含对象：

|       变量       |         类型         |                           作用                           |
| :--------------: | :------------------: | :------------------------------------------------------: |
|   pageContext    |   PageContextImpl    |             它可以获取 jsp 中的九大内置对象              |
|    pageScope     |  Map<String,Object>  |            它可以获取 pageContext 域中的数据             |
|   requestScope   |  Map<String,Object>  |              它可以获取 Request 域中的数据               |
|   sessionScope   |  Map<String,Object>  |              它可以获取 Session 域中的数据               |
| applicationScope |  Map<String,Object>  |           它可以获取 ServletContext 域中的数据           |
|      param       |  Map<String,String>  |                  它可以获取请求参数的值                  |
|   paramValues    | Map<String,String[]> |  它也可以获取请求参数的值，<br />获取多个值的时候使用。  |
|      header      |  Map<String,String>  |                  它可以获取请求头的信息                  |
|   headerValues   | Map<String,String[]> |   它可以获取请求头的信息，<br />它可以获取多个值的情况   |
|      cookie      |  Map<String,Cookie>  |             它可以获取当前请求的 Cookie 信息             |
|    initParam     |  Map<String,String>  | 它可以获取在 web.xml 中配置的`<context-param>`上下文参数 |

##### EL获取四个特定域中的属性:

pageScope        ==        pageContext 域

requestScope        ==        Request 域

sessionScope        ==        Session 域

applicationScope        ==        ServletContext 域

```jsp
<body>
<%
pageContext.setAttribute("key1", "pageContext1");
pageContext.setAttribute("key2", "pageContext2");
request.setAttribute("key2", "request");
session.setAttribute("key2", "session");
application.setAttribute("key2", "application");
%>
${ applicationScope.key2 }
</body>
```



##### pageContext对象的使用:

1. 协议
2. 服务器 ip
3. 服务器端口
4. 获取工程路径
5. 获取请求方法
6. 获取客户端 ip 地址
7. 获取会话的 id 编号

```jsp
<body>
<%--
request.getScheme() 它可以获取请求的协议
request.getServerName() 获取请求的服务器 ip 或域名
request.getServerPort() 获取请求的服务器端口号
getContextPath() 获取当前工程路径
request.getMethod() 获取请求的方式（GET 或 POST）
request.getRemoteHost() 获取客户端的 ip 地址
session.getId() 获取会话的唯一标识
--%>
<%
    pageContext.setAttribute("req", request);
%>
<%=request.getScheme() %> <br>
1.协议： ${ req.scheme }<br>
2.服务器 ip：${ pageContext.request.serverName }<br>
3.服务器端口：${ pageContext.request.serverPort }<br>
4.获取工程路径：${ pageContext.request.contextPath }<br>
5.获取请求方法：${ pageContext.request.method }<br>
6.获取客户端 ip 地址：${ pageContext.request.remoteHost }<br>
7.获取会话的 id 编号：${ pageContext.session.id }<br>
</body>
```



##### 获取请求信息：

```jsp
输出请求参数 username 的值：${ param.username } <br>
输出请求参数 password 的值：${ param.password } <br>

输出请求参数 username 的值：${ paramValues.username[0] } <br>
输出请求参数 hobby 的值：${ paramValues.hobby[0] } <br>
输出请求参数 hobby 的值：${ paramValues.hobby[1] } <br>
```



##### 获取请求头信息：

```jsp
<body>
${header}<br>
${header["accept-language"]}<br>
${header["sec-fetch-dest"]}<br>

${headerValues["user-agent"][0]}
</body>
```



##### 获取cookie值：

```jsp
获取 Cookie 的名称：${ cookie.JSESSIONID.name } <br>
获取 Cookie 的值：${ cookie.JSESSIONID.value } <br>
```



##### 获取web.xml 中的配置：

```jsp
输出&lt;Context-param&gt;username 的值：${ initParam.username } <br>
输出&lt;Context-param&gt;url 的值：${ initParam.url } <br>
```


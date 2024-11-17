## XML

XML是可扩展的标记性语言。



### XML的作用：

1. 用来保存数据，而且这些数据具有自我描述性
2. 它还可以用来做为项目或者模块的配置文件
3. 还可以作为网络传输数据的格式（现在以JSON为主）



### XML的语法：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!--以上为XML文件的声明，version表示版本，encoding表示文件本身编码，必须在第一行-->
<books>
    <book sn="SN1234">
        <name>时间简史</name>
        <author>霍金</author>
        <price>75</price>
    </book>
    <book sn="SN165">
        <name>操作系统</name>
        <author>罗宾</author>
        <price>45</price>
    </book>
</books>
```

> **xml** **中的元素（标签）也 分成 单标签和双标签：**

单标签格式： <标签名 属性=”值” 属性=”值” ...... />

双标签格式：< 标签名 属性=”值” 属性=”值” ......>文本数据或子标签</标签名>

注意点：

1. 一个标签上可以书写多个属性。每个属性的值必须使用 引号 引起来
2. 所有 XML 元素都须有关闭标签（也就是闭合）
3. XML标签对大小写敏感
4. XML 必须正确地嵌套
5. XML 文档必须有根元素
6. XML中的特殊字符(&lt : <           &gt : >)
7. 文本区域（CDATA区）
   格式：`<![CDATA[ 这里可以把你输入的字符原样显示，不会解析 xml ]]>`



### XML的解析：

解析是指读取内容，转化为需要的数据。

不管是 html 文件还是 xml 文件它们都是标记型文档，都可以使用 w3c 组织制定的 dom 技术来解析。

第三方的解析技术：

- jdom   在dom基础上进行封装
- dom4j  对jdom进行封装
- pull  用在 Android 开发



### dom4j 解析技术：

1. 先将dom4j.jar添加到库
2. 用 saxReader 读取 xml 文件到 document
3. 对 document 进行解析获取根元素
4. 通过根元素获取book标签对象
5. 遍历，将每个book标签转化为book类

```java
public void test1() throws DocumentException {
        // 创建一个 saxReader 输入流，去读取xml配置文件，生成document对象
        SAXReader saxReader = new SAXReader();
        // 读取books.xml
        Document document = saxReader.read("XML/books.xml");
        // 获取根元素
        Element rootElement = document.getRootElement();
        // 通过根元素获取book标签对象
        List<Element> books = rootElement.elements("book");
        // 遍历，处理每个book标签为book类
        for (Element book : books) {
            // asXML 是将标签对象转为标签字符串
            // System.out.println(book.asXML());
            // 获取标签
            Element name = book.element("name");
            // 获取属性值
            String sn = book.attributeValue("sn");
            Element author = book.element("author");
            Element price = book.element("price");
            // getText：获取标签中的文本内容
            System.out.println(sn + " " + name.getText() + " " + author.getText() + " " + price.getText());
        }
    }
```


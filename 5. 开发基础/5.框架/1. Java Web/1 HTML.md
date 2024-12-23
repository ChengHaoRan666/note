## HTML

### **基本骨架标签**

```html
<html> </html>   HTML标签，页面中最大的标签，我们称为  根标签

<head> </head>文档的头部，在head中我们必须设置title标签

<title> </title>  文档的标题，让页面拥有一个属于自己的网页标题

<body> </body>  文档的主体，元素包含文档的所有内容，页面内容基本放在body中的
```



### **常见标签 **

```html
1. <!DOCTYPE>	文档类型声明，作用就是告诉浏览器使用那种HTML版本来显示网页。
		例：<!DOCTYPE html>  表示告诉浏览器使用的是html5。

2. <html lang="en">	用于定义当前文档显示的语言。
    	a. en定义语言为英文
		b. zh-CN定义语言为中文
    
3. <meta charset = "UTF-8">  表示当前文件的字符集是UTF-8
    
4. <h1>    一级标题    </h1>
   <h2>    二级标题    </h2>
   <h3>    三级标题    </h3>
   <h4>    四级标题    </h4>
   <h5>    五级标题    </h5>
   <h6>    六级标题    </h6>
   从h1到h6越来越小，都是加粗的，标题都是独占一行的
   有align属性：可取值left(左对齐)，center(居中)，right(右对齐)
    
5. <p>    一段话    </p>   段落标签，将一段话变成一段话
    
6. <br>    换行
    
7. <hr>    水平线
    
8. <strong></strong> 或者 <b></b>    加粗
    
9. <em></em> 或者 <i></i>   倾斜
    
10. <del></del> 或者 <s></s>    删除线
    
11. <ins></ins> 或者 <u></u>    下划线
    
12. <div> </div>      容器，无意义，一个占一行
    
13. <span></span>     容器，无意义，可以多个一行
    
14. <img src = "">     放图片
    src是img的一个属性，其他属性有：
           alt：文本。替换文本，图像不能显示时显示的文字
           title：文本。提示文本，鼠标放到图像上，显示的文字
           width：像素。设置图像的宽度
           height：像素。设置图像的高度
           border：像素。设置图像的边框粗细
    
15. <a> </a>    超链接
    语法格式：<a href = "跳转目标" target = "目标窗口的弹出方式">文本或图像</a>
    属性：
    href：用于指定链接目标的URL地址，（必须属性）
    target：用于指定链接页面的打开方式，其中_self为默认值，_blank为新窗口中打开方式。
    链接分类：
        a.外部链接：其他网址
            例：<a href = "https://www.baidu.com/">百度</a>
               <a href="https://fanyi.baidu.com/" target="_blank">百度翻译</a> 
        b.内部链接：网站内部的相互链接，直接链接文件名即可
            例：<a href="aaa.html" target="_blank">aaa</a>
        c.空链接：其他网页还没有写好，就先搞空链接
            例：<a href="#">空链接</a>
        d.下载链接：如果href里面地址是一个文件或者压缩包，并设置download，会下载这个文件
            例：<a href="aaa.zip" download>下载链接</a>
    	e.锚点链接：当我们点击链接时，可以快速定位到页面中的某个位置。在链接文本的href属性中，		    设置属性值为 #名字 的形式，在目标位置标签里，添加一个id属性=刚刚的名字。
    		例：<a href = "#two">第二季</a>
    		   在第二季的位置上，<h3 id = "two">第二季</h3>
    
16. <iframe> </iframe>    内嵌窗口
    属性：
    src：内嵌的html窗口
    width：宽度
    height：高度
    
    iframe可以和a标签配合使用，设置iframe的name和a的target即可
    <iframe src="hello.html" width="800" height="500" name="iframe1"></iframe>
<ul>
    <li><a href="1.html" target="iframe1">1</a></li>
    <li><a href="2.html" target="iframe1">2</a></li>
    <li><a href="3.html" target="iframe1">3</a></li>
    <li><a href="4.html" target="iframe1">4</a></li>
</ul>
    
```

​	相对路径：

1. 同一级路径：  \<img src = "baidu.gif">

2. 下一级路径：\<img src = "images/baidu.gif">

3. 上一级路径：\<img src = "../baidu.gif">

绝对路径：

​	`http://ip:root/工程名/资源路径`





特殊字符：

![](https://github.com/myself54188/picx-images-hosting/raw/master/特殊字符8.4uausu50bj.webp)



### **表格**

```html
表格：
<table>
    <tr>
        <td>单元格内的文字</td>
    </tr>
</table>

1. <table> </table>是用于定义表格的标签。
2. <tr></tr> 标签用于定义表格中的行，必须嵌套在<table></table>中。
3. <td></td> 用于定义表格中的单元格，必须嵌套在<tr></tr>中。


例：
<table>
    <tr><td>学号</td> <td>姓名</td> <td>成绩</td></tr>
    <tr><td>5001220101</td> <td>蔡豪</td> <td>145</td></tr>
    <tr><td>5001220102</td> <td>陈震</td> <td>1</td></tr>
    <tr><td>5001220103</td> <td>张三</td> <td>14445</td> </tr>
    <tr><td>5001220104</td> <td>李四和</td> <td>1445</td></tr>
</table>

第一行表头很特殊，可以用<th></th>,会自动加粗居中

table属性：
border：设置表格边框宽度

td属性：
colspan：设置单元格占几个宽度
rowspan：设置单元格占几个行

表格结构标签：
因为表格可能很长，为了更好的表示表格的语意，可以将表格分割为表格头和表格主体两大部分，分别用<thead></thead>表示头部区域，<tbody></tbody>表示主体区域。
```





### **列表**

> 表格是用来显示数据的，列表是用来布局的。

> 列表的最大特点就是整齐，整洁，有序。

1. 无序列表：

   ```html
   <ul> 表示HTML页面中项目的无序列表，一般会以项目符号呈现在列表项中，列表项用<li>定义。
       
   <ul>
       <li>列表项1</li>
       <li>列表项2</li>
       <li>列表项3</li>
       <li>列表项4</li>
   </ul>
       
   列表<li></li>会占用一行
   <ul></ul>里只能嵌套<li></li>
   ```

2. 有序列表：

   ```html
   <ol></ol>用于定义有序列表，列表排序以数字显示，并且使用<li></li>定义列表项
   
   <ol>
       <li>列表项1</li>
       <li>列表项2</li>
       <li>列表项3</li>
       <li>列表项4</li>
   </ol>
   ```

3. 自定义列表：

   ```html
   <dl>
       <dt></dt>
       <dd></dd>
       <dd></dd>
   </dl>
   
   <dl></dl>里只能包含<dt></dt>和<dd></dd>
   ```





### **表单**

在HTML中，一个完整的表单通常由<font color = "red">表单域</font>，<font color = "red">表单控件（表单元素）</font>和<font color = "red">提示信息</font>三部分组成。

![](https://github.com/myself54188/picx-images-hosting/raw/master/表单.5fkif4zgm2.webp)

1. 表单域

   ```html
   表单域是一个包含表单元素的区域。
   在html标签中，<form>标签用于定义表单域，以实现用户的收集和传递。
   <form>会把它范围内的表单元素信息提交给服务器。
   
   
   <form action = "url地址"  method = "提交方式"  name = "表单域名称">
   	各种表单元素控件    
   </form>
   
   
   常见属性：
       action:  url地址     用于指定接收并处理表单数据的服务器程序的url地址。
       method:  get/post   用于设置表单数据的提交方式，其取值为get或post。
       name:    名称      用于指定表单的名称，以区分同一个页面中的多个表单域。
       
       
       
   表单提交的时候，数据没有发送给服务器的三种情况：
       1. 表单没有name属性值
       2. 单选，复选（下拉列表中的option标签）都需要添加value值，以便发送给服务器
       3. 表单项不在提交的form标签中
       
       
   get请求和post请求的特点：
       get：
       1. 浏览器地址栏中的地址是：action属性[+？+请求参数]
       2. 不安全
       3. 它有数据长度的限制
       
       post：
       1. 浏览器地址栏中只有action属性值
       2. 相对与get请求要安全
       3. 理论上没有数据长度限制
   ```

   

   

2. 表单控件（表单元素）

   允许用户在表单中输入或者选择内容控件。

   

   1. input输入表单元素

      ```html
      <input type="">
      type是属性值，必须要有。
      ```

      type可以取的值：

      ![](https://github.com/myself54188/picx-images-hosting/raw/master/input表单.175b5b97td.webp)

      

      其他属性：

      ![](https://github.com/myself54188/picx-images-hosting/raw/master/aaaaaa.10239vn2ct.webp)

      

      ```html
      <label></label>标签
      <label></label>标签为input元素定义标注(标签)。
      <label></label>用于绑定一个表单元素，当点击<label>标签内的文本是，浏览器就会自动将焦点（光标）转到或者选择对应的表单元素上，用于增强用户体验。
          
      例：
          <label for = "sex">男</label>
          <input id = "sex" type = "radio" name = "sex">
      ```
   
      

   2. select下拉表达元素

      ```html
      <select>
          <option>选项1</option>
          <option>选项2</option>
          <option>选项3</option>
          <option>选项4</option>
          <option>选项5</option>
          ...
      </select>
      
      <option  selected = "selected"></option>表示把这个选项设置为默认选项
      ```
   
      效果：

      ![](https://github.com/myself54188/picx-images-hosting/raw/master/效果.60u61ftwwy.webp)

      

   3. textarea文本域元素

      ```html
      <textarea>文本框默认文字</textarea>
      可以创建多行文本输入框，在中间输入文字就会是默认文字。
      属性：
      rows：宽度
      cols：长度
      ```
      
      

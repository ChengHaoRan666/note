## Tomcat

### JavaWeb基本概念：

- JavaWeb是指：所有通过 Java 语言编写可以通过浏览器访问的程序的总称，叫 JavaWeb。

- JavaWeb 是基于请求和响应来开发的。 

- 请求是指客户端给服务器发送数据，叫请求 Request。

- 响应是指服务器给客户端回传数据，叫响应 Response。

- 请求和响应是成对出现的，有请求就有响应



### **Web** 资源的分类：

web 资源按实现的技术和呈现的效果的不同，又分为静态资源和动态资源两种。

静态资源： html、css、js、txt、mp4 视频 , jpg 图片

动态资源： jsp 页面、Servlet 程序



### 常用的 Web服务器：

**Tomcat**：由 Apache 组织提供的一种 Web 服务器，提供对 jsp 和 Servlet 的支持。它是一种轻量级的 javaWeb 容器（服务器），也是当前应用最广的 JavaWeb 服务器（免费）。

**Jboss**：是一个遵从 JavaEE 规范的、开放源代码的、纯 Java 的 EJB 服务器，它支持所有 JavaEE 规范（免费）。

**GlassFish**： 由 Oracle 公司开发的一款 JavaWeb 服务器，是一款强健的商业服务器，达到产品级质量（应用很少）。

**Resin**：是 CAUCHO 公司的产品，是一个非常流行的服务器，对 servlet 和 JSP 提供了良好的支持，性能也比较优良，resin 自身采用 JAVA 语言开发（收费，应用比较多）。

**WebLogic**：是 Oracle 公司的产品，是目前应用最广泛的 Web 服务器，支持 JavaEE 规范，

而且不断的完善以适应新的开发要求，适合大型项目（收费，用的不多，适合大公司）。





### tomcat目录介绍：

**bin** ：专门用来存放 Tomcat 服务器的可执行程序

**conf **：专门用来存放 Tocmat 服务器的配置文件

**lib**：专门用来存放 Tomcat 服务器的 jar 包

**logs** ：专门用来存放 Tomcat 服务器运行时输出的日记信息

**temp** ：专门用来存放 Tomcdat 运行时产生的临时数据

**webapps** ：专门用来存放部署的 Web 工程。

**work** ：是 Tomcat 工作时的目录，用来存放 Tomcat 运行时 jsp 翻译为 Servlet 的源码，和 Session 钝化的目录。



### 部署项目到tomcat：

方法一：拷贝到webapps文件夹下，只需访问 `http://ip:root/工程名/目录/文件名`即可

方法二：在conf目录\Catalina\localhost\下，创建 工程名.xml 配置文件

```xml
<!-- Context 表示一个工程上下文
path 表示工程的访问路径:/abc
docBase 表示你的工程目录在哪里
-->
<Context path="/abc" docBase="E:\book" />
```

访问`http://ip:port/abc`即可



### IDEA 整合tomcat服务器：

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713161246300.3nrjk8g3oz.webp)

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713162825131.8z6g4y26d3.webp)



### IDEA创建web项目：

​	![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713162210713.9gwhtj3jxw.webp)

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713182538769.7p3iymk722.webp)

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713162312978.3uurfo294l.webp)

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713162421903.6m3tnqod6e.webp)

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713162535101.2yya07skon.webp)

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713175903489.41xzb3oek8.webp)

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713180507175.86tkn7lkmx.webp)

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240713181908000.51e2o9r5q2.webp) 
---
title: 网络知识学习之HTTP协议(二)
date: 2023-03-25 21:47:34
categories: 计算机网络
---

### 4. HTTP工作过程
一次HTTP操作称为一个事务，其工作整个过程如下：
     1 ) 地址解析如用客户端浏览器请求这个页面：`http://localhost.com:8080/index.html`
从中分解出协议名、主机名、端口、对象路径等部分，对于我们的这个地址，解析得到的结果如下：
     协议名：http
     主机名：localhost.com
     端口：8080
     对象路径：/index.htm
 在这一步，需要域名系统DNS解析域名localhost.com,得主机的IP地址。
2）封装HTTP请求数据包
把以上部分结合本机自己的信息，封装成一个HTTP请求数据包
3）封装成TCP包，建立TCP连接（TCP的三次握手）
在HTTP工作开始之前，客户机（Web浏览器）首先要通过网络与服务器建立连接，该连接是通过TCP来完成的，该协议与IP协议共同构建Internet，即著名的TCP/IP协议族，因此Internet又被称作是TCP/IP网络。HTTP是比TCP更高层次的应用层协议，根据规则，只有低层协议建立之后才能进行更高层协议的连接，因此，首先要建立TCP连接，一般TCP连接的端口号是80。这里是8080端口
4）客户机发送请求命令
建立连接后，客户机发送一个请求给服务器，请求方式的格式为：统一资源标识符（URI：Uniform Resource Identifier）、协议版本号，后边是MIME信息包括请求修饰符、客户机信息和可能的内容。
5）服务器响应
服务器接到请求后，给予相应的响应信息，其格式为一个状态行，包括信息的协议版本号、一个成功或错误的代码，后边是MIME信息包括服务器信息、实体信息和可能的内容。
实体消息是服务器向浏览器发送头信息后，它会发送一个空白行来表示头信息的发送到此结束，接着，它就以Content-Type应答头信息所描述的格式发送用户所请求的实际数据
6）服务器关闭TCP连接
一般情况下，一旦Web服务器向浏览器发送了请求数据，它就要关闭TCP连接，然后如果浏览器或者服务器在其头信息加入了这行代码`Connection:keep-alive`
TCP连接在发送后将仍然保持打开状态，于是，浏览器可以继续通过相同的连接发送请求。保持连接节省了为每个请求建立新连接所需的时间，还节约了网络带宽。

经典面试题【从输入URL到页面加载发生了什么？】
- **DNS解析**：如果地址包含域名通过DNS解析获取服务器对应的ip地址，如果输入的URL不包含域名则不经过这一步。
- **TCP连接** : 经过DNS解析后获取到了服务器的IP地址，在获取到IP地址后，便会开始建立一次连接(三次握手)。
- **发送HTTP请求**:  在确认与服务器建立连接后，便会发送一个HTTP请求。
- **服务器处理请求并返回HTTP报文**: 服务器在收到浏览器发送的HTTP请求之后，会将收到的HTTP报文封装成HTTP的Request对象，并通过不同的Web服务器进行处理，处理完的结果以HTTP的Response对象返回，主要包括状态码，响应头，响应报文三个部分。
- **浏览器解析渲染页面**
- **连接结束**: 在页面元素传输完成后，会选择关闭连接(TCP四次挥手)。

### 5. http报文
**报文(message)**
报文是网络中交换与传输的数据单元，也是网络传输的单元。报文包含了将要发送的完整的数据信息，其长短不需一致。报文在传输过程中会不断地封装成分组、包、帧来传输，封装的方式就是添加一些控制信息组成的首部，那些就是报文头。

http有两种报文
- 请求报文： 请求行、首部字段、报文主体（请求正文）
- 响应报文：状态行、首部字段、报文主体（响应正文）

**请求行**（HTTP请求报文的第一行）
请求行由方法字段、URL字段和HTTP协议版本字段。其中，方法字段严格区分大小写，当前HTTP协议中的方法都是大写。
- 方法字段如下介绍如下（请求方法用来定义队对资源的操作）
1）GET:请求获取Request-URI(URI:通用资源标识符,URL是其子集，URI注重的是标识，而URL强调的是位置，可以将URL看成原始的URI),所标识的资源
2）POST：在Request-URI所标识的资源后附加新的数据；支持HTML表单提交，表单中有用户添入的数据，这些数据会发送到服务器端，由服务器存储至某位置（例如发送处理程序）
3）HEAD:请求Request-URI所标识的资源响应消息报头，HEAD方法可以在响应时不返回消息体。
4）PUT：与GET相反，请求服务器存储一个资源，并用Request-URI做为其标识；例如发布系统。
5）DELETE：请求删除URL指向的资源
6）OPTIONS：请求查询服务器的性能，或者查询与资源相关的选项
7）TRACE：跟踪请求要经过的防火墙、代理或网关等，主要用于测试或诊断
8）CONNECT保留将来使用

**状态行** HTTP响应报文的第一行
状态行包括三个字段：协议版本、状态码与原因短语
-  状态码
状态码由三位数字组成，第一个数字定义了响应的类别， 且由5种取值：
 1）1xx: 指示消息--表示已接收，继续处理 
 2）2xx: 成功--表示请求已被成功接收、理解、接收
 3）3xx: 重定向--要完成请求必须进行更进一步的操作
 4）4xx: 客户端错误--请求有语法错误或请求无法实现
 5）5xx：服务器端错误--服务器端未能实现合法的请求

### 6. 跨域 
**1.&nbsp;什么是跨域** 
&nbsp;&nbsp;&nbsp;&nbsp;浏览器的同源策略限制了跨域请求资源。即当一个请求url的协议、域名、端口三者之间任意一与当前页面地址不同即为跨域。

**2.&nbsp;实现跨域的常用方法**
**(1) &nbsp; jsonp** （JSON+Padding）将JSON数据填充进回调函数
jsonp 是解决跨域问题的一种方案，不同于 json，其并不是一种数据交换格式，而只是一种绕过跨域的技巧。
**简单来说就是创建一个回调函数，然后在远程服务上调用这个函数并且将JSON 数据形式作为参数传递，完成回调。** 因为在HTML标签里，一些标签比如script、img、iframe这样的拥有src属性获取资源的标签是没有跨域限制的。因此利用script加载预设的 callback 将内容传递给 js。一般来说我们约定通过一个参数来告诉服务器 JSONP 返回时应该调用的回调函数名，然后拼接出对应的 js。以下为一个简单例子。
html页面
```
<!DOCTYPE html>
<html>
  <head>
    <title></title>
    <script type="text/javascript">
      var infoHandler = function(data){
          console.log(data);
      };
    </script>
    <script type="text/javascript" src="http://a.com/info?name=a&callback=infoHandler"></script>
  </head>
  <body></body>
</html>
```
服务器端返回
```
# 服务器端通过获取请求参数重的callback名称， 拼出返回值。
 infoHandler({
  "name": "shirley",
  "age":18,
  "gender": "female",
  "idCard": "23213123231232ui323"
});
```
缺点是JSONP只能发GET请求，因为本质上script加载资源就是GET请求

**(2) &nbsp; CORS**
CORS：是一个W3C标准, Cross-origin resource sharing 即 “跨域资源共享”。它允许浏览器向跨源服务器，发出`XMLHttpRequest`请求，从而克服了AJAX只能同源使用的限制。
CORS需要客户端和服务端同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。整个CORS通信过程，都是浏览器自动完成，不需要用户参与。因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。
- CORS请求分类
浏览器将CORS请求分成两类：请求`简单请求`（simple request）和`非简单请求`（not-so-simple request)。
**简单请求：**    请求满足以下两个条件的就是简单请求
&nbsp;1. 请求方法是以下三种方法之一：HEAD,GET,POST 。
&nbsp;2. HTTP的头信息不超出以下几种字段
&nbsp;&nbsp;&nbsp;&nbsp;Accept
&nbsp;&nbsp;&nbsp;&nbsp;Accept-Language
&nbsp;&nbsp;&nbsp;&nbsp;Content-Language
&nbsp;&nbsp;&nbsp;&nbsp;Last-Event-ID
&nbsp;&nbsp;&nbsp;&nbsp;Content-type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain
**非简单请求：** 不满足上面两个条件的就是非简单请求。
**注意** 
1、对于简单请求，浏览器直接请求，会在请求头信息中，增加一个origin字段，来说明本次请求来自哪个源（协议+域名+端口）。服务器根据这个值，来决定是否同意该请求，服务器返回的响应会多几个头信息字段。
2、非简单请求是对那种对服务器有特殊要求的请求，都会在正式通信之前，增加一次HTTP请求，称之为预检（options请求）。浏览器会先询问服务器，当前网页所在域名是否在服务器的许可名单之中，服务器允许之后，浏览器会发出正式的XMLHttpRequest请求，否则会报错。
**options请求**:  options请求是浏览器自发起的preflight request(预检请求)，获取响应后发现可以跨域，接着就发送真实的请求。

* 服务器端实现跨域CORS接口

1、设置`Access-Control-Allow-Origin`
```
'Access-Control-Allow-Origin': 'http://XXX.com' // 设置接指定地址的请求或设置为'*' 表示接受任意域名的请求
```
2. 除get、post、head请求方法和其它自定义的请求头 即非简单请求时。预请求验证通过才能发送。
```
'Access-Control-Allow-Methods': 'POST,PUT,HEAD'
```
```
'Access-Control-Allow-Headers': 'X-Test-Cors', //设置自定义的请求头
```
设置缓存, 允许浏览器在指定时间内，无需再发送预检请求，直接用本次结果即可。
```
'Access-Control-Max-Age': '5',  //秒为单位 -1是不缓存 5秒一下chrome都默认是5
```
设置是否可以携带cookie
```
'Access-Control-Allow-Credentials':  true，//如果Access-Control-Allow-Origin字段设置* 此字段设为true无效
```



**(3) nginx反向代理** 
原理是：同源策略只是浏览器的安全策略，不是HTTP协议的一部分。服务器端调用HTTP接口只是使用HTTP协议，不会执行JS脚本，不需要同源策略，也就不存在跨越问题。
  ```
  // Nginx配置如下：
   server{    
    # 监听8001端口
    listen 8001;
    # 域名是localhost
    server_name localhost;
    #凡是localhost:8001/api这个样子的，都转发到真正的服务端地址http://localhost:9001
    location ^~ /api {
        proxy_pass http://localhost:9001;
    } 
```

***To be continued...***
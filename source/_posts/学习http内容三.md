---
title: 网络知识学习之HTTP协议（三)
date: 2023-04-10 21:53:05
categories: 计算机网络
---

### 7. 首部字段
上面的HTTP报文介绍了首部字段。首部字段同时存在于请求和响应报文内，并涵盖 HTTP 报文相关的内容信息。使用首部字段是为了给客服端和服务器端提供报文主体大小、所使用的语言、认证信息等内容。

- **首部字段结构**
由首部字段名和字段值，中间用冒号分割。 例如 Content-Type：text/html
**注** :  HTTP 报文首部中出现了两个或以上具有相同首部字段名的首部字段时，这种情况在规范内尚未明确，根据浏览器内部处理逻辑的不同，优先处理的顺序可能不同，结果可能并不一致。
- **首部字段类型**
HTTP 首部字段根据实际用途被分为以下 4 种类型：
**1. 通用首部字段**（General Header Fields）
请求报文和响应报文两方都会使用的首部。
**2. 请求首部字段**（Request Header Fields）
从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息。
**3. 响应首部字段**（Response Header Fields）
从服务器端向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。
**4. 实体首部字段**（Entity Header Fields）
针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等与实体有关的信息。
- **通用首部字段** (HTTP/1.1)
Cache-Control	  控制缓存的行为
Connection	  逐挑首部、连接的管理
Date	创建报文的日期时间
Pragma	报文指令
Trailer	报文末端的首部一览
Transfer-Encoding	指定报文主体的传输编码方式
Upgrade	升级为其他协议
Via	代理服务器的相关信息
Warning	错误通知

- **请求首部字段**
Accept                         用户代理可处理的媒体类型
Accept-Charset            优先的字符集
Accept-Encoding         优先的内容编码
Accept-Language        优先的语言（自然语言）
Authorization               Web认证信息
Expect                          期待服务器的特定行为
From                             用户的电子邮箱地址
Host                              请求资源所在服务器
If-Match                        比较实体标记（ETag）
If-Modified-Since          比较资源的更新时间
If-None-Match              比较实体标记（与 If-Match 相反）
If-Range                        资源未更新时发送实体 Byte 的范围请求
If-Unmodified-Since     比较资源的更新时间（与If-Modified-Since相反）
Max-Forwards               最大传输逐跳数
Proxy-Authorization     代理服务器要求客户端的认证信息
Range                           实体的字节范围请求
Referer                          对请求中 URI 的原始获取方
TE                                  传输编码的优先级
User-Agent                   HTTP 客户端程序的信息

-  **响应首部字段** 
Accept-Ranges             是否接受字节范围请求
Age                               推算资源创建经过时间
ETag                              资源的匹配信息
Location                        令客户端重定向至指定URI
Proxy-Authenticate      代理服务器对客户端的认证信息
Retry-After                   对再次发起请求的时机要求
Server HTTP                  服务器的安装信息
Vary                              代理服务器缓存的管理信息
WWW-Authenticate     服务器对客户端的认证信息


-  **实体首部字段** 
Allow                            资源可支持的HTTP方法
Content-Encoding       实体主体适用的编码方式
Content-Language      实体主体的自然语言
Content-Length           实体主体的大小（单位：字节）
Content-Location        替代对应资源的URI
Content-MD5              实体主体的报文摘要
Content-Range            实体主体的位置范围
Content-Type              实体主体的媒体类型
Expires                         实体主体过期的日期时间
Last-Modified              资源的最后修改日期时间

##### 7.1 Cache-Control
通过指定首部字段 Cache-Control 的指令，就能操作缓存的工作机制。指令的参数是可选的，多个指令之间通过“,”分隔。比如： Cache-Control: private, max-age=0, no-cache​。
- 缓存请求指令
![缓存请求指令.png](/images/image16.png)
- 缓存响应指令
![缓存响应指令.png](/images/image17.png)

Pragma和Expires也与缓存相关，Pragma是旧产物，已经逐步抛弃，有些网站为了向下兼容还保留了这两个字段。优先级从高到低是:Pragma -> Cache-Control -> Expires

Cache-Control除了在响应中使用，在请求中也可以使用。我们用开发者工具来模拟下请求时带上Cache-Control：勾选Disable cache，刷新页面，可以看到Request Headers中有个字段Cache-Control: no-cache。

##### 7.2 Connection
Connection 首部字段具备以下两个作用：

1. 控制不再转发的首部字段
Connection: Upgrade
在客户端发送请求和服务器返回响应中，使用 Connection 首部字段，可控制不再转发给代理的首部字段，即删除后再转发（即Hop-by-hop首部）。
![747969-20170217214935269-1562584482.png](/images/image18.png)
2. 管理持久连接
- Connection: close
HTTP/1.1 版本的默认连接都是持久连接。当服务器端想明确断开连接时，则指定 Connection 首部字段的值为 close。
- Connection: Keep-Alive
HTTP/1.1 之前的 HTTP 版本的默认连接都是非持久连接。为此，如果想在旧版本的 HTTP 协议上维持持续连接，则需要指定 Connection 首部字段的值为 Keep-Alive。

##### 7.3 Date
Date 表明创建 HTTP 报文的日期和时间

##### 7.4  Accept
`Accept: text/html, application/xhtml+xml, application/xml; q=0.5`
- Accept 首部字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级。可使用 type/subtype 这种形式，一次指定多种媒体类型。
- 若想要给显示的媒体类型增加优先级，则使用 q=[数值] 来表示权重值，用分号（;）进行分隔。权重值的范围 0~1（可精确到小数点后三位），且 1 为最大值。不指定权重值时，默认为 1。当服务器提供多种内容时，将会首先返回权重值最高的媒体类型。

##### 7.5  Accept-Encoding
`Accept-Encoding: gzip, deflate`
Accept-Encoding 首部字段用来告知服务器用户代理支持的内容编码及内容编码的优先顺序，并可一次性指定多种内容编码。同样使用 q=[数值] 来表示相对优先级。也可使用星号（*）作为通配符，指定任意的编码格式。

##### 7.6  Accept-Language
Accept-Lanuage: zh-cn,zh;q=0.7,en=us,en;q=0.3
告知服务器用户代理能够处理的自然语言集（指中文或英文等），以及自然语言集的相对优先级，可一次性指定多种自然语言集。同样使用 q=[数值] 来表示相对优先级。

##### 7.7 Host
`Host: localhost:8001​`
首部字段 Host 会告知服务器，请求的资源所处的互联网主机名和端口号。Host 首部字段在 HTTP/1.1 规范内是唯一一个必须被包含在请求内的首部字段。
首部字段 Host 和以单台服务器分配多个域名的虚拟主机的工作机制有很密切的关联，这是首部字段 Host 必须存在的意义

##### 7.8 User-AgentUser-Agent
`User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_4) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36`
- 首部字段 User-Agent 会将创建请求的浏览器和用户代理名称等信息传达给服务器。
- 由网络爬虫发起请求时，有可能会在字段内添加爬虫作者的电子邮件地址。此外，如果请求经过代理，那么中间也很可能被添加上代理服务器的名称。

##### 7.9 Allow
`Allow: GET, HEAD`
- 首部字段 Allow 用于通知客户端能够支持 Request-URI 指定资源的所有 HTTP 方法。
- 当服务器接收到不支持的 HTTP 方法时，会以状态码 405 Method Not Allowed 作为响应返回。与此同时，还会把所有能支持的 HTTP 方法写入首部字段 Allow 后返回。

##### 7.10 Content-Encoding
`Content-Encoding: gzip`
- 首部字段 Content-Encoding 会告知客户端服务器对实体的主体部分选用的内容编码方式。内容编码是指在不丢失实体信息的前提下所进行的压缩。
- 主要采用这 4 种内容编码的方式（gzip、compress、deflate、identity）。

##### 7.11 Content-Language
`Content-Language: zh-CN`
首部字段 Content-Language 会告知客户端，实体主体使用的自然语言（指中文或英文等语言）。

##### 7.12 Content-Type
`Content-Type: text/html; charset=UTF-8`
首部字段 Content-Type 说明了实体主体内对象的媒体类型。和首部字段 Accept 一样，字段值用 type/subtype 形式赋值。参数 charset 使用 iso-8859-1 或 euc-jp 等字符集进行赋值。

##### 7.13  Expires
`Expires: Mon, 10 Jul 2017 15:50:06 GMT`

- 首部字段 Expires 会将资源失效的日期告知客户端。
- 缓存服务器在接收到含有首部字段 Expires 的响应后，会以缓存来应答请求，在 Expires 字段值指定的时间之前，响应的副本会一直被保存。当超过指定的时间后，缓存服务器在请求发送过来时，会转向源服务器请求资源。
- 源服务器不希望缓存服务器对资源缓存时，最好在 Expires 字段内写入与首部字段 Date 相同的时间值。

#####7.14 Cookie
 服务器在响应头中用Set-Cookie头将Cookie的内容回送给客户端，客户端在新的请求中将相同的内容携带在cookie头中发送给服务器，从而实现会话的保持。
- 响应首部字段
`setCookie: name=aaa; expires=Mon, 10 Jul 2017 15:50:06 GMT; path=/`
属性	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;说明
NAME=VALUE&nbsp;&nbsp;&nbsp;&nbsp;赋予 Cookie 的名称和其值（必需项）
expires=DATE&nbsp;&nbsp;&nbsp;&nbsp;Cookie 的有效期（若不明确指定则默认为浏览器关闭前为止）
path=PATH&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;将服务器上的文件目录作为Cookie的适用对象（若不指定则默认为文档所在的文件目录）
domain=域名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;作为 Cookie 适用对象的域名 （若不指定则默认为创建 Cookie的服务器的域名）
Secure&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;仅在 HTTPS 安全通信时才会发送 Cookie
HttpOnly&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;加以限制，使 Cookie 不能被 JavaScript 脚本访问
- 请求首部字段 Cookie
Cookie: status=enable

首部字段 Cookie 会告知服务器，当客户端想获得 HTTP 状态管理支持时，就会在请求中包含从服务器接收到的 Cookie。接收到多个 Cookie 时，同样可以以多个 Cookie 形式发送。

##### 7.15  CSP(content-security-policy)内容安全策略
实质是建立报名单制度，明确的告诉客户端，哪些外部资源可以加载和执行。所以使用 CSP 来防止 XSS攻击(跨域脚本攻击)。

`Content-Security-Policy： 'default-src \'self\' `表示只能加载同域下资源。
`
Content-Security-Policy： 'default-src \'self\'; report-uri /report'` 表示只能加载同域下资源, 且会发送一个违例报告。默认情况下，违规报告并不会发送。为启用发送违规报告，你需要指定 report-uri策略指令，并提供至少一个URI地址去递交报告。
如果我只想收集报告，但是不真正的去限制请求，那怎么办？除了Content-Security-Policy，还有一个`Content-Security-Policy-Report-Only`字段，表示不执行限选项，只是记录违反限制的行为。将头部改为这个即可。
**注**： `default-src`设置的是全局，如果我只想限制js的请求，可以将default-src改为script-src, 限制图片请求可用`img-src`

使用meta标签达到一样的效果。
`<meta http-equiv="Content-Security-Policy" content="default-src 'self'">`

详细用法可以参考[Content Security Policy](https://www.ruanyifeng.com/blog/2016/09/csp.html)

#####7.16  Sec-Fetch-*
简单来说，就是网络请求的元数据描述，服务端根据这些补充数据进行细粒度的控制响应。浏览器自动带上的请求头，都是Forbidden header，也就是不能被篡改的。
`Sec-Fetch-Dest`: 期望需要什么样的资源,比如 script。
`Sec-Fetch-Mode`： 表明了一个请求的模式，比如是否跨域。
`Sec-Fetch-Site`: 请求发起者的来源与目标资源来源之间的关系，比如跨域就为 `cross-site`。

##### 7.17 其它首部字段
HTTP 首部字段是可以自行扩展的。所以在 Web 服务器和浏览器的应用上，会出现各种非标准的首部字段。
例如:  `X-Frame-Options`: 用于控制网站内容在其他 Web 网站的 iframe 标签内的显示问题。

### 8. TCP长连接
- 短连接的操作步骤是：
建立连接——数据传输——关闭连接...建立连接——数据传输——关闭连接
- 长连接的操作步骤是：
建立连接——数据传输...（保持连接）...数据传输——关闭连接

- 持久的连接节省通信
每次进行HTTP请求的时候，都要先建立TCP连接，然后结束之后再断开TCP连接。这样如果在同一份HTML文档中有大量的图片等资源，就会建立和断开多次TCP连接，造成资源的浪费。HTTP1.1和一部分HTTP1.0想出了持久连接。持久连接的特点：只要任意一端没有明确断开连接，就保持TCP的链接状态。这样不会因为频繁的建立和断开TCP连接造成额外的开销，减轻了服务器的负载。同时减少了HTTP请求和响应的时间。
![持久连接.png](/images/image19.png)

- TCP connection
 Connection: keep-alive/close(开启/关闭)
HTTP2只需要建立一个TCP长连接（同域下）

那是不是只需要建立一个TCP连接，然后发送多个HTTP请求就可以了？ 不行。
HTTP/1.1 存在一个问题：单个 TCP 连接在同一时刻只能处理一个请求。如果一个网页上有多个资源需要请求，肯定不能只开一个TCP连接，然后按请求顺序下载，那样用户肯定等的很难受。
所以Chrome最多允许对同一个 Host 建立六个 TCP 连接。（不同的浏览器允许的连接数不同。就解释了之前我们所了解到的的浏览器请求的最大并发量是6个(以chrome为例)。

相关文章
[CORS](https://www.jianshu.com/p/c68fb8f75f38)
[JSONP](https://www.jianshu.com/p/e1e2920dac95)
[HTTP](https://www.jianshu.com/p/6e9e4156ece3)
[你知道一个TCP连接上能发起多少个HTTP请求吗？](https://zhuanlan.zhihu.com/p/76302817)

***http基本知识学习完啦...***


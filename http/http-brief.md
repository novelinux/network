## HTTP协议用于客户端和服务器端之间的通信

HTTP协议和TCP/IP协议族内的其他众多的协议相同，用于客户端和服务器之间的通信。
请求访问文本或图像等资源的一端称为客户端，而提供资源响应的一端称为服务器端。

在两台计算机之间使用HTTP协议通信时，在一条通信线路上必定有一端是客户端，另一端则是服务器端。
有时候，按实际情况，两台计算机作为客户端和服务器端的角色有可能会互换。但就仅从一条通信路线来说，
服务器端和客户端的角色是确定的，而用HTTP协议能够明确区分哪端是客户端，哪端是服务器端。

## 通过请求和响应的交换达成通信

HTTP协议规定，请求从客户端发出，最后服务器端响应该请求并返回。换句话说，肯定是先从客户端开始建立
通信的，服务器端在没有接收到请求之前不会发送响应。下面，我们来看一个具体的示例。

[C/S](https://github.com/novelinux/network/blob/master/http/res/client-server.jpeg "C/S")

下面则是从客户端发送给某个HTTP服务器端的请求报文中的内容。

```
GET /index.htm HTTP/1.1
Host: hackr.jp
```

起始行开头的GET表示请求访问服务器的类型，称为方法（method）。随后的字符串/index.htm指明了请求访
问的资源对象，也叫做请求URI（request-URI）。最后的HTTP/1.1，即HTTP的版本号，用来提示客户端使用
的HTTP协议功能。

综合来看，这段请求内容的意思是：请求访问某台HTTP服务器上的/index.htm页面资源。

请求报文是由请求方法、请求URI、协议版本、可选的请求首部字段和内容实体构成的。

请求首部字段及内容实体稍后会作详细说明。接下来，我们继续讲解。接收到请求的服务器，会将请求内容的
处理结果以响应的形式返回。

```
HTTP/1.1 200 OK
Date: Tue, 10 Jul 2012 06:50:15 GMT
Content-Length: 362
Content-Type: text/html<html>
……
```

在起始行开头的HTTP/1.1表示服务器对应的HTTP版本。紧挨着的200 OK表示请求的处理结果的状态码
（statuscode）和原因短语（reason-phrase）。下一行显示了创建响应的日期时间，是首部字段
（header field）内的一个属性。接着以一空行分隔，之后的内容称为资源实体的主体（entitybody）。
响应报文基本上由协议版本、状态码（表示请求成功或失败的数字代码）、用以解释状态码的原因短语、
可选的响应首部字段以及实体主体构成。稍后我们会对这些内容进行详细说明。

## HTTP是不保存状态的协议

HTTP是一种不保存状态，即无状态（stateless）协议。HTTP协议自身不对请求和
响应之间的通信状态进行保存。也就是说在HTTP这个级别，协议对于发送过的请求或响应都不做持久化处理。
HTTP协议自身不具备保存之前发送过的请求或响应的功能使用HTTP协议，每当有新的请求发送时，就会有对应
的新响应产生。协议本身并不保留之前一切的请求或响应报文的信息。这是为了更快地处理大量事务，确保协议
的可伸缩性，而特意把HTTP协议设计成如此简单的。可是，随着Web的不断发展，因无状态而导致业务处理变得
棘手的情况增多了。比如，用户登录到一家购物网站，即使他跳转到该站的其他页面后，也需要能继续保持登录
状态。针对这个实例，网站为了能够掌握是谁送出的请求，需要保存用户的状态。HTTP/1.1虽然是无状态协议
，但为了实现期望的保持状态功能，于是引入了Cookie技术。有了Cookie再用HTTP协议通信，就可以管理状
态了。有关Cookie的详细内容稍后讲解。

## 请求URI定位资源

HTTP协议使用URI定位互联网上的资源。正是因为URI的特定功能，在互联网上任意位置的资源都能访问到。

[Http URI](https://github.com/novelinux/network/blob/master/http/res/http-uri.jpeg "Http URI")

当客户端请求访问资源而发送请求时，URI需要将作为请求报文中的请求URI包含在内。指定请求URI的方式有很多。

除此之外，如果不是访问特定资源而是对服务器本身发起请求，可以用一个*来代替请求URI。
下面这个例子是查询HTTP服务器端支持的HTTP方法种类。

```
OPTIONS * HTTP/1.1
```

## 告知服务器意图的HTTP方法

### GET：获取资源

GET方法用来请求访问已被URI识别的资源。指定的资源经服务器端解析后返回响应内容。也就是说，如果请求
的资源是文本，那就保持原样返回；如果是像CGI（Common Gateway In-terface，通用网关接口）那样的
程序，则返回经过执行后的输出结果。

[Http GET](https://github.com/novelinux/network/blob/master/http/res/http-get-sum.jpeg "Http GET")
使用GET方法的请求
[Http GET](https://github.com/novelinux/network/blob/master/http/res/http-get.jpeg "Http GET")

```
$ http GET example.org
HTTP/1.1 200 OK
Cache-Control: max-age=604800
Content-Encoding: gzip
Content-Length: 606
Content-Type: text/html; charset=UTF-8
Date: Tue, 26 Feb 2019 07:11:21 GMT
Etag: "1541025663+gzip"
Expires: Tue, 05 Mar 2019 07:11:21 GMT
Last-Modified: Fri, 09 Aug 2013 23:54:35 GMT
Server: ECS (dcb/7EEF)
Vary: Accept-Encoding
X-Cache: HIT
```

### POST：传输实体主体

POST方法用来传输实体的主体。虽然用GET方法也可以传输实体的主体，但一般不用GET方法进行传输，而是用
POST方法。虽说POST的功能与GET很相似，但POST的主要目的并不是获取响应的主体内容。

[Http POST](https://github.com/novelinux/network/blob/master/http/res/http-post.jpeg "Http POST")

### PUT：传输文件

PUT方法用来传输文件。就像FTP协议的文件上传一样，要求在请求报文的主体中包含文件内容，然后保存到请
求URI指定的位置。但是，鉴于HTTP/1.1的PUT方法自身不带验证机制，任何人都可以上传文件，存在安全性问
题，因此一般的Web网站不使用该方法。若配合Web应用程序的验证机制，或架构设计采用REST
（REpresentational State Transfer，表征状态转移）标准的同类Web网站，就可能会开放使用PUT方法。

[Http PUT](https://github.com/novelinux/network/blob/master/http/res/http-put.jpeg "Http PUT")

### HEAD：获得报文首部

HEAD方法和GET方法一样，只是不返回报文主体部分。用于确认URI的有效性及资源更新的日期时间等。

[Http HEAD](https://github.com/novelinux/network/blob/master/http/res/http-head.jpeg "Http HEAD")

### DELETE：删除文件

DELETE方法用来删除文件，是与PUT相反的方法。DELETE方法按请求URI删除指定的资源。但是，HTTP/1.1的
DELETE方法本身和PUT方法一样不带验证机制，所以一般的Web网站也不使用DELETE方法。当配合Web应用程
序的验证机制，或遵守REST标准时还是有可能会开放使用的。

[Http DELETE](https://github.com/novelinux/network/blob/master/http/res/http-delete.jpeg "Http DELETE")

### OPTIONS：询问支持的方法

OPTIONS方法用来查询针对请求URI指定的资源支持的方法。

[Http OPTIONS](https://github.com/novelinux/network/blob/master/http/res/http-options.jpeg "Http OPTIONS")

```
$ http OPTIONS example.org
HTTP/1.1 200 OK
Allow: OPTIONS, GET, HEAD, POST
Cache-Control: max-age=604800
Content-Length: 0
Content-Type: text/html; charset=UTF-8
Date: Tue, 26 Feb 2019 07:07:41 GMT
Expires: Tue, 05 Mar 2019 07:07:41 GMT
Server: EOS (vny006/0450)
```

### TRACE：追踪路径

TRACE方法是让Web服务器端将之前的请求通信环回给客户端的方法。发送请求时，在Max-Forwards首部字段
中填入数值，每经过一个服务器端就将该数字减1，当数值刚好减到0时，就停止继续传输，最后接收到请求的服
务器端则返回状态码200 OK的响应。客户端通过TRACE方法可以查询发送出去的请求是怎样被加工修改/篡改的
。这是因为，请求想要连接到源目标服务器可能会通过代理中转，TRACE方法就是用来确认连接过程中发生的一
系列操作。

但是，TRACE方法本来就不怎么常用，再加上它容易引发XST（Cross-Site Tracing，跨站追踪）攻击，通常就更不会用到了。

```
$ http TRACE hackr.jp
HTTP/1.1 200 OK
Connection: Keep-Alive
Content-Type: message/http
Date: Tue, 26 Feb 2019 07:23:56 GMT
Keep-Alive: timeout=15, max=100
Server: Apache
Transfer-Encoding: chunked

TRACE / HTTP/1.1
Host: hackr.jp
User-Agent: HTTPie/0.9.8
Accept-Encoding: gzip, deflate
Accept: */*
Connection: keep-alive
Content-Length: 0
```

### CONNECT：要求用隧道协议连接代理

CONNECT方法要求在与代理服务器通信时建立隧道，实现用隧道协议进行TCP通信。主要使用SSL（Secure
SocketsLayer，安全套接层）和TLS（Transport Layer Security，传输层安全）协议把通信内容加密后
经网络隧道传输。CONNECT方法的格式如下所示。

```
CONNECT代理服务器名:端口号HTTP版本
```

## 使用方法下达命令

方法的作用在于，可以指定请求的资源按期望产生某种行为。方法中有GET、POST和HEAD等。

下表列出了HTTP/1.0和HTTP/1.1支持的方法。另外，方法名区分大小写，注意要用大写字母。

[Http FUNCTIONS](https://github.com/novelinux/network/blob/master/http/res/http-functions.jpeg "Http FUNCTIONS")

在这里列举的众多方法中，LINK和UNLINK已被HTTP/1.1废弃，不再支持。

## 持久连接节省通信量

HTTP协议的初始版本中，每进行一次HTTP通信就要断开一次TCP连接。

[Http TCP Link](https://github.com/novelinux/network/blob/master/http/res/http-link.jpeg "Http TCP LINK")

以当年的通信情况来说，因为都是些容量很小的文本传输，所以即使这样也没有多大问题。可随着HTTP的普及，
文档中包含大量图片的情况多了起来。比如，使用浏览器浏览一个包含多张图片的HTML页面时，在发送请求访
问HTML页面资源的同时，也会请求该HTML页面里包含的其他资源。因此，每次的请求都会造成无谓的TCP连接
建立和断开，增加通信量的开销。

### 持久连接

为解决上述TCP连接的问题，HTTP/1.1和一部分的HTTP/1.0想出了持久连接（HTTP Persistent Connections，
也称为HTTP keep-alive或HTTP connection reuse）的方法。持久连接的特点是，只要任意一端没有明确
提出断开连接，则保持TCP连接状态。

持久连接的好处在于减少了TCP连接的重复建立和断开所造成的额外开销，减轻了服务器端的负载。另外，减少
开销的那部分时间，使HTTP请求和响应能够更早地结束，这样Web页面的显示速度也就相应提高了。在HTTP/1.
1中，所有的连接默认都是持久连接，但在HTTP/1.0内并未标准化。虽然有一部分服务器通过非标准的手段实现
了持久连接，但服务器端不一定能够支持持久连接。毫无疑问，除了服务器端，客户端也需要支持持久连接。

### 管线化

持久连接使得多数请求以管线化（pipelining）方式发送成为可能。从前发送请求后需等待并收到响应，才能
发送下一个请求。管线化技术出现后，不用等待响应亦可直接发送下一个请求。这样就能够做到同时并行发送多
个请求，而不需要一个接一个地等待响应了。

比如，当请求一个包含10张图片的HTML Web页面，与挨个连接相比，用持久连接可以让请求更快结束。而管线
化技术则比持久连接还要快。请求数越多，时间差就越明显。

## 使用Cookie的状态管理

HTTP是无状态协议，它不对之前发生过的请求和响应的状态进行管理。也就是说，无法根据之前的状态进行本
次的请求处理。假设要求登录认证的Web页面本身无法进行状态的管理（不记录已登录的状态），那么每次跳转
新页面不是要再次登录，就是要在每次请求报文中附加参数来管理登录状态。不可否认，无状态协议当然也有它
的优点。由于不必保存状态，自然可减少服务器的CPU及内存资源的消耗。从另一侧面来说，也正是因为HTTP协
议本身是非常简单的，所以才会被应用在各种场景里。

保留无状态协议这个特征的同时又要解决类似的矛盾问题，于是引入了Cookie技术。Cookie技术通过在请求和
响应报文中写入Cookie信息来控制客户端的状态。

Cookie会根据从服务器端发送的响应报文内的一个叫做Set-Cookie的首部字段信息，通知客户端保存Cookie。
当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入Cookie值后发送出去。服务器端发现
客户端发送过来的Cookie后，会去检查究竟是从哪一个客户端发来的连接请求，然后对比服务器上的记录，最
后得到之前的状态信息。

[没有Cookie信息状态下的请求](https://github.com/novelinux/network/blob/master/http/res/http-cookie1.jpeg "Http")

[第2次以后（存有Cookie信息状态）的请求](https://github.com/novelinux/network/blob/master/http/res/http-cookie2.jpeg "Http")

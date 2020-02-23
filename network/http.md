# HTTP 协议



## 什么是 HTTP ？

**HTTP（HyperText Transfer Protocol，超文本传输协议）** 是客户端和服务器端之间的一种数据传输协议。

协议就是计算机与计算机之间通过网络实现通信时事先达成的一种“约定”。这种“约定”使那些由不同厂商的设备、不同的 CPU 以及不同的操作系统组成的计算器之间，只要遵循相同的协议就能够实现通信。

HTTP 属于 OSI 参考模型中的 **应用层协议** 。其他应用层协议还有 **SMTP** 、**FTP**、**TELNET/SSH**、**SNMP** 。

> HTTP 通常被译为超文本传输协议，但这种译法并不严谨。严谨的译名应该为 “超文本转移协议”。但是前移译法已经约定俗成。



## HTTP 版本

* HTTP/0.9

  HTTP 的 1991 原型版本称为 HTTP/0.9 。这个协议有很多严重的设计缺陷，只用于和老客户端的交互。HTTP/0.9 只支持 GET 方法，不支持多媒体内容的 MIME 类型、各种 HTTP 首部，或者版本号。HTTP/0.9 定义的初衷是为了获取简单的 HTML ，很快就被 HTTP/1.0 代替了。

* HTTP/1.0

  1.0 是第一个得到广泛使用的 HTTP 版本。HTTP/1.0 添加了版本号、各种 HTTP 首部、一些额外的方法，以及对多媒体对象的处理。

  其他的新增功能还包括状态码（status code）、多字符集支持、多部分发送（multi-part type）、权限（authorization）、缓存（cache）、内容编码（content encoding）等。

  但是 1.0 版本的工作方式是每次 TCP 连接只能发送一个请求，当服务器响应后就会关闭这次连接，下一个请求需要再次建立 TCP 连接，就是不支持 keep-alive 。

* HTTP/1.1

  1.1 是目前主流的 HTTP 协议版本，重点校正了 HTTP 设计中的结构性缺陷，明确语义，引入重要的性能优化措施，并删除一些不好的特性。

  最大的变化就是引入了持久连接，即 TCP 连接默认不关闭，可以被多个请求复用，不用声明`Connection: keep-alive` ，一个 TCP 连接允许多个 HTTP 请求。

* HTTP/2.0

  HTTP/2.0 是 HTTP 协议自 1999 年 HTTP/1.1 发布后的首个更新，主要基于 SPDY 协议，在不改动HTTP语义、方法、状态码、URI 及首部字段的情况下，大幅度提高了Web 性能 。它具有如下特点：

  * 二进制传输。HTTP/1.x 版本采用的是文本格式
  * 多路复用。解决旧版本的对头阻塞(Head of line blocking) 问题，一个 TCP 连接中存在多个流，即可以发送多个请求
  * header 压缩。减小 header 大小
  * 服务器 Push。服务端可以在客户端某个请求后，主动推送其他资源

参考资料 ： [【快速入门系列】一篇文章让你入门HTTP2.0](https://segmentfault.com/a/1190000016656529)](https://segmentfault.com/a/1190000016656529)



## URI、URL、URN

**URI，Uniform Resource Identifier ，统一资源标识符** 。用来唯一标识并定位资源。它是一个通用的概念，由两个主要的子集 URL 和 URN 构成。

**URL，Uniform Resource Locator ，统一资源定位符** 。URL 描述了一台特定服务器上某资源的特定位置。我们常见的各种网址就是典型的 URL ，一般遵循通用语法：

```
协议类型://服务器地址[:端口号]/路径
```

以我的个人博客的关于页面为例，https://luyao.tech/s/me ，协议类型是 `http` ，服务器地址是 `luyao.tech` ，资源路径是 `/s/me` 。

**URN , Uniform Resource Name ,  统一资源名** 。与 URL 使用资源路径定位不同，URN 使用特定内容的唯一名称来定位，与资源位置无关，这样就可以将资源四处搬移。但是从 URL 转换到 URN 是一项巨大的工程，目前为止并没有多大进展。



## 报文格式

**请求报文**



```
<method> <request-URL> <version>
<headers>

<entity-body>
```



![请求报文](http://cdn.luyao.tech/%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87.png)

**响应报文**



```
<version> <status> <reason-phase>
<headers>

<entity-body>
```



![响应报文](http://cdn.luyao.tech/%E5%93%8D%E5%BA%94%E6%8A%A5%E6%96%87.png)



## 请求方法

**GET** 方法用来请求访问已被 URI 识别的资源。指定的资源经服务器端解析后返回响应内容。不修改服务器数据。**支持 1.0、1.1 版本 。**

**POST** 方法用来向服务器传输数据，会增加或者修改服务器数据。要传输的数据写在 body 中。**支持 1.0、1.1 版本 。**

**PUT** 方法会向服务器写入文档。它的语义就是让服务器用请求的 body 部分来创建一个由所请求的 URL 命名的新文档，如果已存在，就代替它。**支持 1.0、1.1 版本 。**

**HEAD** 方法与 GET 类似，但是服务器只会返回首部，不会返回 body 。**支持 1.0、1.1 版本 。** 可以：

* 在不获取资源的情况下了解资源的情况（比如，判断其类型）
* 通过查看响应中的状态码，查看资源是否存在
* 通过查看首部，测试资源是否被修改

**DELETE** 方法用于删除指定资源。**支持 1.0、1.1 版本 。**

**OPTIONS** 方法请求 Web 服务器告知其支持的各种功能。可以询问服务器通常支持哪些方法，或者对某些特殊资源支持哪些方法。**仅支持 1.1 版本 。**

**TRACE** 方法可以让服务器将之前的请求通信返回给客户端。客户端通过 TRACE 方法可以查询发送出去的请求是怎样被加工修改/篡改的。主要用于诊断。**仅支持 1.1 版本 。**

**CONNECT** 方法要求在与代理服务器通信时建立隧道,实现用隧道协议进行 TCP 通信。主要使用 SSL(Secure Sockets Layer,安全套接层)和 TLS(Transport Layer Security,传输层安全)协议把通信内容
加密后经网络隧道传输。**仅支持 1.1 版本 。**



## 状态码

**1xx ，信息性状态码 **

| 状态码 |      原因短语       |                            含义                            |
| :----: | :-----------------: | :--------------------------------------------------------: |
|  100   |      Continue       |          收到了请求的起始部分，客户端应该继续请求          |
|  101   | Switching Protocols | 服务端正根据客户端的指示将协议切换成 Update 首部列出的协议 |



**2xx，成功状态码**

| 状态码 |           原因短语            |                             含义                             |
| :----: | :---------------------------: | :----------------------------------------------------------: |
|  200   |              OK               |                     服务器已成功处理请求                     |
|  201   |            Created            |       对那些要服务器创建对象的请求来说，资源已创建完毕       |
|  202   |           Accepted            |                 请求已接受，但服务器尚未处理                 |
|  203   | Non-Authoritative Information | 服务器已将事务成功处理，只是实体首部包含的信息不是来原始服务器，而是来自资源的副本 |
|  204   |          No Content           |   响应报文包含一些首部和一个状态行，但不包含实体的主体内容   |
|  205   |         Reset Content         | 主要用于浏览器，意思是浏览器应该重置当前页面上的所有 HTML 表单 |
|  206   |        Partial Content        |                         部分请求成功                         |

206 表示客户端进行了范围请求，而服务器成功执行了这部分的 GET 请求。响应报文中包含由 Content-Range 指定范围的实体内容。



**3xx，重定向状态码**

| 状态码 |      原因短语      |                             含义                             |
| :----: | :----------------: | :----------------------------------------------------------: |
|  300   |  Multiple Choices  | 客户端请求一个实际指向多个资源的 URL 时会返回这个状态码，这个代码和一个选项表一起返回，供用户进行选择 |
|  301   | Moved Permanently  | 永久性重定向。请求的 URL 已移走，响应的 Location 首部应该包含资源现在的 URL |
|  302   |       Found        | 临时重定向。客户端应该使用 Location 首部给出的 URL 来临时定位资源，将来的请求仍应使用老的 URL |
|  303   |     See Other      | 由于请求对应的资源存在着另一个 URL，应使用 GET 方法定向获取请求的资源 |
|  304   |    Not Modified    | 客户端发送附带条件的请求时，服务器资源未改变，可直接使用客户端未过期的缓存 |
|  305   |     Use Proxy      |    必须通过代理来访问资源，代理的位置由 Location 首部给出    |
|  307   | Temporary Redirect |               临时重定向，与 302 有相同的含义                |

304 中附带条件的请求是指采用 **GET** 方法的请求报文中包含 **If-Match** ，**If-Modified-Since** ，**If-None-Match**，**If-Range**，**If-Unmodified-Since** 中任一首部。  

对于 HTTP/1.1 客户端，用 307 状态码取代 302 状态码进行临时重定向，307 会遵照浏览器标准，不会从 POST 变成 GET。这样服务器可以将 302 状态码保留起来，为 HTTP/1.0 客户端使用。很多现存的浏览器会将 302 响应视为 303 响应，并且使用 GET 方法访问在 Location 中规定的 URL，而无视原来的请求方法。



**4xx，客户端错误状态码**

| 状态码 |            原因短语             |                             含义                             |
| :----: | :-----------------------------: | :----------------------------------------------------------: |
|  400   |           Bad Request           |                    请求报文中存在语法错误                    |
|  401   |          Unauthorized           | 在客户端获取资源访问权之前，需要进行身份认证。响应中必须包含一个适用于被请求资源的 WWW-Authenticate 首部用以质询用户信息 |
|  402   |        Payment Required         |                         未使用，预留                         |
|  403   |            Forbidden            |                       请求被服务器拒绝                       |
|  404   |            Not Found            |                   服务器无法找到请求的资源                   |
|  405   |       Method Not Allowed        | 发起的请求中带有所请求的 URL 不支持的方法。响应中应包含 Allow 首部，以告诉客户端请求的资源可以使用哪些方法 |
|  406   |         Not Acceptable          | 客户端可以指定一些参数爱说明希望接受哪些类型的实体，服务器没有资源与客户端可接受的 URL 相匹配时使用此状态码 |
|  407   |  Proxy Authentication Required  |       与 401 类似，但用于需要进行资源认证的代理服务器        |
|  408   |         Request Timeout         | 如果客户端完成其请求时花费的时间太长，服务器可以返回此状态码关闭连接 |
|  409   |            Conflict             |  请求可能在资源上引发一些冲突。响应中应该包含描述冲突的主体  |
|  410   |              Gone               |           与 404 类似，只是服务器曾经拥有过此资源            |
|  411   |         Length Required         |         服务器要求请求报文中包含 Content-Length 首部         |
|  412   |       Precondition Failed       |     客户端发起了条件请求，且其中一个条件失败了的时候使用     |
|  413   |     Request Enity Too Large     |    客户端发送的实体主体部分比服务器能够或者希望处理的要大    |
|  414   |      Request URI Too Long       |   客户端诉所发请求中的 URL 比服务器能够或者希望处理的要长    |
|  415   |     Unsupported Media Type      |       服务器无法理解或无法支持客户端所发实体的内容类型       |
|  416   | Requested Range Not Satisfiable | 请求报文所请求的是指定资源的某个范围，而此范围无效或无法满足 |
|  417   |       Expectation Failed        | 请求报文的 Expect 首部包含了一个期望，但服务器无法满足此期望 |



**5xx，服务器错误状态码**

| 状态码 |          原因短语          |                             含义                             |
| :----: | :------------------------: | :----------------------------------------------------------: |
|  500   |   Internal Server Error    |                 服务器在执行请求时发生了错误                 |
|  501   |      Not Implemented       | 客户端发起的请求超出了服务器的能力范围（比如不支持的请求方法） |
|  502   |        Bad Gateway         |  作为代理或网关使用的服务器遇到了来自响应链中上游的无效响应  |
|  503   |    Service Unavailable     | 服务器暂时无法为请求提供服务。如果事先得知恢复需要的时间，最好写入 Retry-After 首部返回给客户端 |
|  504   |      Gateway Timeout       | 与 408 类似，只是这里的响应来自一个网关或者代理，它们在等待另一服务器对其请求进行响应时超时了 |
|  505   | HTTP Version Not Supported |       服务器收到的请求使用了它无法或不愿支持的协议版本       |



## 报文首部

HTTP 协议的请求和响应报文中必定包含 HTTP 首部。首部内容为客户端和服务器分别处理请求和响应提供所需要的信息。



HTTP 首部字段是由首部字段名和字段值构成的,中间用冒号“:” 分隔。

> 首部字段名: 字段值

HTTP 首部字段根据实际用途被分为以下 4 种类型：**通用首部字段**，**请求首部字段**，**响应首部字段**，**实体首部字段** 。

### 通用首部字段

| 首部字段名 | 说明 |
| :--: | :--: |
| Cache-Control | 控制缓存的行为 |
| Connection | 逐跳首部、连接的管理 |
| Date | 创建报文的日期时间 |
| Pragma | 报文指令 |
| Trailer | 报文末端的首部一览 |
| Transfer-Encoding | 指定报文主体的传输编码方式 |
| Upgrade | 升级为其他协议 |
| Via | 代理服务器的相关信息 |
| Warning | 错误通知 |

#### Cache-Control

控制缓存的工作机制。

|       指令       |                             含义                             |
| :--------------: | :----------------------------------------------------------: |
|      public      |                    其他用户也可以利用缓存                    |
|     private      |                  对特定用户提供资源缓存服务                  |
|     no-cache     | 并不是不缓存，缓存会向源服务器进行有效期确认后处理资源，防止从缓存中返回过期的资源 |
|     no-store     |             不缓存，暗示请求或响应中包含机密信息             |
|  max-age = [秒]  | 当客户端发送的请求中包含 max-age 指令时,如果判定缓存资源的缓存时间数值比指定时间的数值更小,那么客户端就接收缓存的资源。另外,当指定 max-age 值为 0,那么缓存服务器通常需要将请求转发给源服务器。<br/>当服务器返回的响应中包含 max-age 指令时,缓存服务器将不对资源的有效性再作确认,而 max-age 数值代表资源保存为缓存的最长时间。 |
| min-fresh = [秒] | 要求缓存服务器返回至少还未过指定时间的缓存资源。当指定 min-fresh 为 60 秒后,过了 60 秒的资源都无法作为响应返回了 |
| max-stale = [秒] | 如果指令未指定参数值,那么无论经过多久,客户端都会接收响应; 如果指令中指定了具体数值,那么即使过期,只要仍处于 max-stale 指定的时间内,仍旧会被客户端接收。 |
|  only-if-cached  |        只有缓存中有副本存在时，客户端才会获取一份副本        |
| must-revalidate  |  代理会向源服务器再次验证即将返回的响应缓存目前是否仍然有效  |
| proxy-revalidate | 要求所有的缓存服务器在接收到客户端带有该指令的请求返回响应之前,必须再次验证缓存的有效性 |
|   no-transform   | 无论是在请求还是响应中,缓存都不能改变实体主体的媒体类型。可以防止缓存或代理压缩图片等类似操作 |

#### Connection

Connection 首部有两个作用，控制不再转发给代理的首部字段和管理持久连接。

应用程序收到带有 Connection 首部的报文后，会删除报文中所有在 Connection 首部列表中出现过的首部。主要用于代理网络环境，这样服务器或其他代理就可以指定不应传递的逐跳首部了。

HTTP/1.1 版本的默认连接都是持久连接。为此, 客户端会在持久连接上连续发送请求。当服务器端想明确断开连接时,则指定 Connection 首部字段的值为 **Close** 。

HTTP/1.1 之前的 HTTP 版本的默认连接都是非持久连接。为此, 如果想在旧版本的 HTTP 协议上维持持续连接,则需要指定 Connection 首部字段的值为 **Keep-Alive** 。

#### Date

创建 HTTP 报文的日期和时间

#### Pragma

Pragma 是 HTTP/1.1 之前版本的历史遗留字段,仅作为与 HTTP/1.0 的向后兼容而定义。规范定义的形式唯一,如下所示 ：

```
Pragma: no-cache
```

表示客户端要求所有的中间服务器不返回缓存的资源。

#### Trailer

如果报文采用了分块传输编码（chunked transfer encoding）方式，就可以使用 Trailer 列出位于报文末端的首部。

#### Transfer-Encoding

规定了传输报文主体时采用的编码方式。HTTP/1.1 的传输编码方式仅对分块传输编码有效。

> HTTP/1.1 200 OK
> Date: Tue, 03 Jul 2012 04:40:56 GMT
> Cache-Control: public, max-age=604800
> Content-Type: text/javascript; charset=utf-8
> Expires: Tue, 10 Jul 2012 04:40:56 GMT
> X-Frame-Options: DENY
> X-XSS-Protection: 1; mode=block
> Content-Encoding: gzip
> **Transfer-Encoding: chunked**
> Connection: keep-alive
> cf0	←16进制(10进制为3312)
> ...3312字节分块数据...
> 392	←16进制(10进制为914)
> ...914字节分块数据...
> 0

分别被分成 3312 字节和 914 字节大小的分块数据 。

#### Upgrade

用于检测 HTTP 协议及其他协议是否可使用更高的版本进行通信, 其参数值可以用来指定一个完全不同的通信协议。对于附有首部字段 Upgrade 的请求,服务器可用 **101 Switching Protocols** 状态码作为响应返回。

#### Via

使用首部字段 Via 是为了追踪客户端与服务器之间的请求和响应报文的传输路径。报文经过代理或网关时, 会先在首部字段 Via 中附加该服务器的信息, 然后再进行转发。

#### Warning

HTTP/1.1 的 Warning 首部是从 HTTP/1.0 的响应首部(Retry-After)演变过来的。该首部通常会告知用户一些与缓存相关的问题的警告。

### 请求首部字段

| 首部字段名          | 说明                                        |
| ------------------- | ------------------------------------------- |
| Accept              | 用户代理可处理的媒体类型                    |
| Accept-Charset      | 优先的字符集                                |
| Accept-Encoding     | 优先的内容编码                              |
| Accept-Language     | 优先的语言(自然语言)                        |
| Authorization       | Web认证信息                                 |
| Expect              | 期待服务器的特定行为                        |
| From                | 用户的电子邮箱地址                          |
| Host                | 请求资源所在服务器                          |
| If-Match            | 比较实体标记(ETag)                          |
| If-Modified-Since   | 比较资源的更新时间                          |
| If-None-Match       | 比较实体标记(与 If-Match 相反)              |
| If-Range            | 资源未更新时发送实体 Byte 的范围请求        |
| If-Unmodified-Since | 比较资源的更新时间(与If-Modified-Since相反) |
| Max-Forwards        | 最大传输逐跳数                              |
| Proxy-Authorization | 代理服务器要求客户端的认证信息              |
| Range               | 实体的字节范围请求                          |
| Referer             | 对请求中 URI 的原始获取方                   |
| TE                  | 传输编码的优先级                            |
| User-Agent          | HTTP 客户端程序的信息                       |

#### Accept

通知服务器, 用户代理能够处理的媒体类型及媒体类型的相对优先级。

```
Accept: text/html,application/xhtml+xml,application/xml;q=0
```

q 表示权重值，范围是 0-1 (可精确到小数点后 3 位) ，1 为最大值，默认值为 1 。当服务器提供多种内容时,将会首先返回权重值最高的媒体类型。

#### Accept-Charset

通知服务器用户代理支持的字符集及字符集的相对优先顺序 。

```
Accept-Charset: iso-8859-5, unicode-1-1;q=0.8
```

#### Accept-Encoding

告知服务器用户代理支持的内容编码及内容编码的优先级顺序。常见内容编码，**gzip**，**compress**，**deflate**，**identity** 。通配符 `*` 号可指定任意的编码格式。

####  Accept-Language

告知服务器用户代理能够处理的自然语言集(指中文或英文等),以及自然语言集的相对优先级。

```
Accept-Language: zh-cn,zh;q=0.7,en-us,en;q=0.3
```

#### Authorization

告知服务器,用户代理的认证信息(证书值)。通常,想要通过服务器认证的用户代理会在接收到返回的 401 状态码响应后,把首部字段 Authorization 加入请求中。

```
Authorization: Basic dWVub3NlbjpwYXNzd29yZA==
```

#### Expect

客户端使用首部字段 Expect 来告知服务器,期望出现的某种特定行为。因服务器无法理解客户端的期望作出回应而发生错误时,会返回状态码 417 Expectation Failed。

```
Expect: 100-continue
```

#### From

用来告知服务器使用用户代理的用户的电子邮件地址。通常, 其使用目的就是为了显示搜索引擎等用户代理的负责人的电子邮件联系方式。

#### Host

单台服务器分配多个域名的虚拟主机时，就要靠 Host 首部加以区分。

```
Host: luyao.tech
```

#### If-Match

服务器会比对 If-Match 的字段值和资源的 ETag 值, 仅当两者一致时, 才会执行请求。反之, 则返回状态码 **412 Precondition Failed** 的响应。

#### If-None-Match

用于指定 If-None-Match 字段值的实体标记(ETag)值与请求资源的 ETag 不一致时, 它就告知服务器处理该请求。与 If-Match 正好相反。

#### If-Modified-Since

告知服务器若 If-Modified-Since 字段值早于资源的更新时间,则希望能处理该请求。而在指定 If-Modified-Since 字段值的日期时间之后, 如果请求的资源都没有过更新, 则返回状态码 304 Not Modified 的响应。

#### If-Unmodified-Since

指定的请求资源只有在 If-Unmodified-Since 字段值内指定的日期时间之后, 未发生更新的情况下, 才能处理请求。如果在指定日期时间后发生了更新, 则以状态码 412 Precondition Failed 作为响应返回。与 If-Modified-Since 正好相反。

#### If-Range

告知服务器若指定的 If-Range 字段值(ETag 值或者时间)和请求资源的 ETag 值或时间相一致时, 则作为范围请求处理。反之, 则返回全体资源。

#### Max-Forwards

通过 TRACE 方法或 OPTIONS 方法,发送包含首部字段 Max-Forwards 的请求时,该字段以十进制整数形式指定可经过的服务器最大数目。服务器在往下一个服务器转发请求之前,Max-Forwards 的值减 1 后重新赋值。当服务器接收到 Max-Forwards 值为 0 的请求时, 则不再进行转发, 而是直接返回响应。

```
Max-Forwards: 10
```

#### Proxy-Authorization

接收到从代理服务器发来的认证质询时,客户端会发送包含首部字段 Proxy-Authorization 的请求, 以告知服务器认证所需要的信息。

这个行为是与客户端和服务器之间的 HTTP 访问认证相类似的,不同之处在于, 认证行为发生在客户端与代理之间。客户端与服务器之间的认证, 使用首部字段 Authorization 可起到相同作用。

#### Range

```
Range: bytes=5001-10000
```

对于只需获取部分资源的范围请求, 包含首部字段 Range 即可告知服务器资源的指定范围。上面的示例表示请求获取从第 5001 字节至第10000 字节的资源。
接收到附带 Range 首部字段请求的服务器,会在处理请求之后返回状态码为 206 Partial Content 的响应。无法处理该范围请求时, 则会返回状态码 200 OK 的响应及全部资源。

#### Referer

首部字段 Referer 会告知服务器请求的原始资源的 URI。
客户端一般都会发送 Referer 首部字段给服务器。但当直接在浏览器的地址栏输入 URI, 或出于安全性的考虑时, 也可以不发送该首部字段。

```
Referer: http://www.hackr.jp/index.htm
```

#### TE

告知服务器客户端能够处理响应的传输编码方式及相对优先级。它和首部字段 Accept-Encoding 的功能很相像, 但是用于传输编码。

```
TE: gzip, deflate;q=0.5
```

#### User-Agent

首部字段 User-Agent 会将创建请求的浏览器和用户代理名称等信息传达给服务器。

```
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:13.0) Gecko/2010010
```



### 响应首部字段

| 首部字段名         | 说明                         |
| ------------------ | ---------------------------- |
| Accept-Ranges      | 是否接受字节范围请求         |
| Age                | 推算资源创建经过时间         |
| ETag               | 资源的匹配信息               |
| Location           | 令客户端重定向至指定URI      |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
| Retry-After        | 对再次发起请求的时机要求     |
| Server             | HTTP服务器的安装信息         |
| Vary               | 代理服务器缓存的管理信息     |
| WWW-Authenticate   | 服务器对客户端的认证信息     |

#### Accept-Ranges

用来告知客户端服务器是否能处理范围请求, 以指定获取服务器端某个部分的资源。

可指定的字段值有两种, 可处理范围请求时指定其为 bytes, 反之则指定其为 none。

```
Accept-Ranges: bytes
Accept-Ranges: none
```

#### Age

告知客户端源服务器在多久前创建了响应。字段值的单位为秒。

#### ETag

首部字段 ETag 能告知客户端实体标识。它是一种可将资源以字符串形式做唯一性标识的方式。服务器会为每份资源分配对应的 ETag 值。资源更新时，ETag 值也会需要更新。

#### Location

将响应接收方引导至某个与请求 URI 位置不同的资源。基本上都是配合 3xx 重定向使用。

#### Proxy-Authenticate

把由代理服务器所要求的认证信息发送给客户端。

它与客户端和服务器之间的 HTTP 访问认证的行为相似, 不同之处在于其认证行为是在客户端与代理之间进行的。而客户端与服务器之间进行认证时, 首部字段 WWW-Authorization 有着相同的作用。

#### Retry-After

告知客户端应该在多久之后再次发送请求。主要配合状态码 503 Service Unavailable 响应, 或 3xx Redirect 响应一起使用。

字段值可以指定为具体的日期时间(Wed, 04 Jul 2012 06:34:24 GMT 等格式), 也可以是创建响应后的秒数。

#### Server

告知客户端当前服务器上安装的 HTTP 服务器应用程
序的信息。

```
Server: Apache/2.2.17 (Unix)
```

#### Vary

当代理服务器接收到带有 Vary 首部字段指定获取资源的请求时, 如果使用的 Accept-Language 字段的值相同, 那么就直接从缓存返回响应。反之, 需要先从源服务器端获取资源后才能作为响应返回。

```
Vary: Accept-Language
```

#### WWW-Authenticate

用于 HTTP 访问认证。它会告知客户端适用于访问请求 URI 所指定资源的认证方案(Basic 或是 Digest)和带参数提示的质询(challenge)。状态码 401 Unauthorized 响应中, 肯定带有首部字段 WWW-Authenticate。

```
WWW-Authenticate: Basic realm="Usagidesign Auth"
```



### 实体首部字段

| 首部字段名       | 说明                      |
| ---------------- | ------------------------- |
| Allow            | 资源可支持的HTTP方法      |
| Content-Encoding | 实体主体适用的编码方式    |
| Content-Language | 实体主体的自然语言        |
| Content-Length   | 实体主体的大小(单位:字节) |
| Content-Location | 替代对应资源的URI         |
| Content-MD5      | 实体主体的报文摘要        |
| Content-Range    | 实体主体的位置范围        |
| Content-Type     | 实体主体的媒体类型        |
| Expires          | 实体主体过期的日期时间    |
| Last-Modified    | 资源的最后修改日期时间    |

#### Allow

通知客户端能够支持 Request-URI 指定资源的所有 HTTP 方法。当服务器接收到不支持的 HTTP 方法时,会以状态码 405 Method Not Allowed 作为响应返回。与此同时, 还会把所有能支持的 HTTP 方法写入首部字段 Allow 后返回。

```
Allow: GET, HEAD
```

#### Content-Encoding

告知客户端服务器对实体的主体部分选用的内容编码方式，主要有四种，**gzip**，**compress**，**deflate**，**identity** 。

#### Content-Language

告知客户端, 实体主体使用的自然语言。

#### Content-Length

实体主体部分的大小(单位是字节)。对实体主体进行内容编码传输时, 不能再使用 Content-Length
首部字段。

#### Content-Location

首部字段 Content-Location 给出与报文主体部分相对应的 URI。和首部字段 Location 不同, Content-Location 表示的是报文主体返回资源对应的 URI。

#### Content-MD5

首部字段 Content-MD5 是一串由 MD5 算法生成的值, 其目的在于检查报文主体在传输过程中是否保持完整, 以及确认传输到达。客户端会对接收的报文主体执行相同的 MD5 算法, 然后与首部字段 Content-MD5 的字段值比较。

#### Content-Range

针对范围请求, 返回响应时使用的首部字段 Content-Range, 能告知客户端作为响应返回的实体的哪个部分符合范围请求。字段值以字节为单位, 表示当前发送部分及整个实体大小。

```
Content-Range: bytes 5001-10000/10000
```

#### Content-Type

实体主体内对象的媒体类型。

```
Content-Type: text/html; charset=UTF-8
```

#### Expires

首部字段 Expires 会将资源失效的日期告知客户端。缓存服务器在接收到含有首部字段 Expires 的响应后, 会以缓存来应答请求, 在 Expires 字段值指定的时间之前,响应的副本会一直被保存。当超过指定的时间后, 缓存服务器在请求发送过来时, 会转向源服务器请求资源。

源服务器不希望缓存服务器对资源缓存时, 最好在 Expires 字段内写入与首部字段 Date 相同的时间值。但是, 当首部字段 Cache-Control 有指定 max-age 指令时, 比起首部字段 Expires, 会优先处理 max-age 指令。

#### Last-Modified

首部字段 Last-Modified 指明资源最终修改的时间。一般来说, 这个值就是 Request-URI 指定资源被修改的时间。
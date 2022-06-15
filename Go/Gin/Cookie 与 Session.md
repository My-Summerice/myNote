# Cookie 与 Session

### 如何解决HTTP无状态协议

在 Web 应用中跟踪用户状态的办法有以下四种：

- 建立包含有跟踪数据的隐藏字段（比如使用`<input type="hidden">`）
- 重写包含额外参数的URL（文末会介绍）
- 使用持续的 Cookie
- 使用 Session

Session 和 Cookie 是 Web 程序中较为常见的两个概念。它们的目的相同，都是为了克服 http 协议无状态的问题。

### Cookie

Cookie 实际上是一小段由客户端（如浏览器）保存在本地的文本文件，他记录了你的用户ID、密码、浏览过的网页、浏览时间等信息。当客户端再次访问同一网站时，客户端把请求和 Cookie 一起发送给服务端，服务端通过检验该 Cookie ，就可以辨认用户状态了。

##### 特点

- Cookie  中主要包含了NAME（名称）、path（路径）、domain（域名）、expires（有效期）、max-age（过期时间）几种不同的属性，它们各自起到不同的作用。

- 例如通过设置 Cookie 的 maxAge 属性可以设置 Cookie 的过期时间，**不设置 maxAge** 被称为会话 Cookie，会话 Cookie  生命周期为从创建浏览器到关闭浏览器为止，只要关闭浏览器窗口，会话 Cookie 就会消失，**它一般保存在内存中，而不是硬盘中**。

- 如果**设置了过期时间**（setMaxAge(60\*60*24)）,**浏览器就会把 Cookie 保存在硬盘上**，对于关闭后重新打开的浏览器，这些 Cookie 依旧有效。

	cookie过期时间设置方式：

	cookie.setMaxAge(0);//不记录cookie

	cookie.setMaxAge(-1);//会话级cookie，关闭浏览器失效

	cookie.setMaxAge(60*60);//过期时间为1小时

### Session

除了使用 Cookie 之外，Web 应用程序还经常使用 Session 来跟踪用户状态，与 Cookie 保存在客户端浏览器不同的是，Session 是将状态信息保存在了服务端上。服务端使用**hash表**来保存信息。

##### session的工作原理

1. 客户端第一次向服务器端发送请求时，服务端程序会为此客户端创建一个session，并生成一个与此session相关联的sessionId。（sessionId 的值应该是一个不会重复并且难以伪造的字符串）
2. 服务端向客户端返回响应时，同时会将 sessionId 一起返回给客户端，客户端会将 sessionId 字符串保存下来。
3. 当客户端再次访问服务端时，将 sessionId 一并发送给服务端。
4. 服务端获取从客户端发送过来的 sessionId，就可以根据这个 id 获取保存在服务器中相应的数据了
5. 当其他客户端也访问服务端时，就又会产生一个新的sessionId，类似以上的步骤进行处理。

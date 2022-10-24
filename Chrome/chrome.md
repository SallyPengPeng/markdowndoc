进程：一个进程就是一个程序的运行实例。详细解释就是，启动一个程序的时候，操作系统会为该程序创建一块内存，用来存放代码、运行中的数据和一个执行任务的主线程，我们把这样的一个运行环境叫进程。

进程和线程的关系：

1. 进程中的任意一线程执行出错，都会导致整个进程的崩溃。
2. 线程之间共享进程中的数据
3. 当一个进程关闭之后，操作系统会回首进程所占用的内存
4. 进程之间的内容互相隔离



浏览器主进程 GPU进程 网络进程 渲染进程 插件进程



网络协议 http web socket 都是基于TCP/IP



物理层 数据链路层 网络层 传输层 会话层 表示层 应用层



互联网中的数据是通过数据包来传输的，数据包在传输过程中容易丢失或出错。

IP负责把数据包送达目的主机。

UDP User Datagram Protocol负责把数据包送达具体应用。

TCP Transmission Control Protocol保证了数据完整的传输：建立连接、传输数据、断开连接

## HTTP

hypertext transport protocol 超文本传输协议，详细规定了浏览器和服务器之间互相通信的规则

**HTTPS和HTTP的主要区别**

- HTTPS是HTTP协议的安全版本，HTTP的数据传输是明文，不安全，HTTPS使用SSL/TLS协议进行了加密处理，更安全
- HTTP 和 HTTPS 使用连接方式不同，默认端口也不一样，HTTP是80，HTTPS是443
- HTTPS 由于需要设计加密以及多次握手，性能方面不如 HTTP
- HTTPS需要SSL，SSL 证书需要钱，功能越强大的证书费用越高

### 请求

- get。查询请求，会有缓存，body为空

- PUT请求。向服务器端发送数据，从而修改数据的内容，但是不会增加数据的种类等。更新数据

- POST请求。向服务器端发送数据，该请求会改变数据的种类等资源，它会创建新的内容。创建数据

### request

| header                             |                                                              |                                                              |
| ---------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Connection                         | 该浏览器想要优先使用的连接类型                               | Connection: Keep-Alive <br />close                           |
| accept: text/html                  | 期望服务器返回 html 类型的文件                               |                                                              |
| accept-encoding: gzip, deflate, br | 期望服务器可以采用 gzip、deflate 或者 br 其中的一种压缩方式  |                                                              |
| accept-Charset: ISO-8859-1,utf-8   | 期望返回的文件编码是 UTF-8 或者 ISO-8859-1                   |                                                              |
| accept-language: zh-CN,zh          | 期望页面的优先语言是中文                                     |                                                              |
| host                               | 当前的域名地址                                               |                                                              |
| referer                            | 提供访问来源信息，相当于购买时的推荐人。比如用户点击网页上的链接时 |                                                              |
| Cache-Control                      | 用来指定在这次的请求/响应链中的所有缓存机制 都必须 遵守的指令 | Cache-Control: no-cache                                      |
| Cookie                             | 服务器通过 Set- Cookie （下文详述）发送的一个 超文本传输协议Cookie | Cookie: $Version=1; Skin=new;                                |
| If-Modified-Since                  | 允许在对应的内容未被修改的情况下返回304未修改                | If-Modified-Since: Sat, 29 Oct 1994 19:43:31 GMT             |
| User-Agent                         | 浏览器的浏览器身份标识字符串                                 | User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:12.0) Gecko/20100101 Firefox/21.0 |
| Origin                             | 发起一个针对 跨来源资源共享 的请求                           | Origin: url                                                  |

### response

| header                                 |                                                              |
| -------------------------------------- | ------------------------------------------------------------ |
| content-encoding: br                   | 服务器采用了 br 的压缩方法                                   |
| content-type: text/html; charset=UTF-8 | 服务器返回的是 html 文件，并且该文件的编码类型是 UTF-8       |
| location: http://www/geekbang.org/     | 状态码是301，表示需要重定向，重定向 的网址包含在响应头的`location`字段 |
| Set-Cookie: UID=3431uad;               | 浏览器就会把这个字段信息保存到cookie                         |
| server                                 | 服务器的地址                                                 |

### 返回码

2xx 代表请求已成功被服务器接收、理解、并接受

- 200（SUCCESS） 正常返回数据

3XX 表示要完成请求，需要进一步操作。 通常，这些状态代码用来重定向

- 301：重定向。会缓存。新域名替换旧域名，旧的域名不再使用时，用户访问旧域名时用301就重定向到新的域名
- 302：临时重定向不会缓存，常用 于未登陆的用户访问用户中心重定向到登录页面
- 304：协商缓存，告诉客户端有缓存，直接使用缓存中的数据，返回页面的只有头部信息，是没有内容部分

4XX 代表了客户端看起来可能发生了错误，妨碍了服务器的处理

- 400（BAD_REQUEST） 客户端它发送了一个错误的请求(一般是参数不合法，可以在校验异常中使用)

- 401（UNAUTHORIZED） 没有认证(一般是没有登录)

- 403（FORBIDDEN）请求被服务器拒绝了(一般是认证通过了,但是没有权限访问该资源)

- 404（NOT_FOUND） 服务器无法找到所请求的 URL(一般在 Controller 中使用)

- 405（METHOD_NOT_ALLOWED） 发起的请求中带有所请求的 URL 不支持的方法时(如果该方法仅支持 POST 请求，用 GET 请求则报错)

- 408 （REQUEST_TIMEOUT）请求超时(客户端完成请求所花的时间太长)

5XX 表示服务器无法完成明显有效的请求。这类状态码代表了服务器在处理请求的过程中有错误或者异常状态发生

- 500（ERROR） 服务器异常(一般数处理逻辑出现异常,可以在 service 中使用)
- 502（错误网关）： 服务器作为网关或代理，从上游服务器收到无效响应
- 503（服务不可用）： 服务器目前无法使用（由于超载或停机维护）
- 504（网关超时）： 服务器作为网关或代理，但是没有及时从上游服务器收到请求

### Timing

<img src="https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-14-ba91f06503bda4b4dc4a54901bd7a8af-7d560deedb30dd2144d60255c12138b0-d3f.png" alt="img" style="zoom: 67%;" />

1. Resource Scheduling

   Queueing：排队等待。导致排队等待原因很多，例如：

   - 资源优先级：比如 CSS、HTML、JavaScript 等都是页面中的核心文件，所以优先级最高；而图片、视频、音频这类资源就不是核心资源，优先级就比较低。通常当后者遇到前者时，就需要“让路”，进入待排队状态。
   - TCP连接数目：浏览器会为每个域名最多维护 6 个 TCP 连接，如果发起一个 HTTP 请求时，这 6 个 TCP 连接都处于忙碌状态，那么这个请求就会处于排队状态。
   - 网络进程在为数据分配磁盘空间时，新的 HTTP 请求也需要短暂地等待磁盘分配结束。

2. Connection Start

   1. Stalled：连接停滞。在发起连接之前，还有一些原因可能导致连接过程被推迟
   2. Proxy Negotiation：代理协商阶段，它表示代理服务器连接协商所用的时间
   3. Initial connection/SSL：和服务器建立连接，包括建立 TCP 连接；如果使用 HTTPS 协议，还需额外的 SSL 握手时间，主要用来协商一些加密信息的

3. Request/Response：和服务器建立好连接之后，网络进程会准备请求数据，并将其发送给网络

   1. Request sent：把浏览器缓冲区的数据发送出去，不需要判断服务器是否接收到，通常不到1ms
   2. Waiting (TTFB)：“第一字节时间”。等待接收服务器第一个字节的数据
   3. Content Download：从第一字节时间到接收到全部响应数据所用的时间

### 优化时间线上耗时项

1. 排队（Queuing）时间过久排队时间过久，大概率是由浏览器为每个域名最多维护 6 个连接导致的。可以让 1 个站点下面的资源放在多个域名下面，比如放到 3 个域名下面，这样就可以同时支持 18 个连接了，这种方案称为域名分片技术。除了域名分片技术外，还可以把站点升级到 HTTP2，因为 HTTP2 已经没有每个域名最多维护 6 个 TCP 连接的限制了。
2. 第一字节时间（TTFB）时间过久。这可能的原因及解决方法如下：
   - 服务器生成页面数据的时间过久。对于动态网页来说，服务器收到用户打开一个页面的请求时，首先要从数据库中读取该页面需要的数据，然后把这些数据传入到模板中，模板渲染后，再返回给用户。解决方法：想办法去提高服务器的处理速度，比如通过增加各种缓存的技术
   - 服务器在处理这个数据的过程中，可能某个环节会出问题。网络的原因。比如使用了低带宽的服务器，或者本来用的是电信的服务器，可联通的网络用户要来访问你的服务器，这样也会拖慢网速。解决方法：可以使用 CDN 来缓存一些静态文件
   - 发送请求头时带上了多余的用户信息。比如一些不必要的 Cookie 信息，服务器接收到这些 Cookie 信息之后可能需要对每一项都做处理，这样就加大了服务器的处理时长。解决方法：在发送请求时就去尽可能地减少一些不必要的 Cookie 数据信息。
3. Content Download 时间过久。如果单个请求的 Content Download 花费了大量时间，有可能是字节数太多的原因导致的。这时候你就需要减少文件大小，比如压缩、去掉源码中不必要的注释等方法。

## 浏览器存储

https://juejin.cn/post/6844903945253421069

本地存储特性：

- 数据存储在用户浏览器中
- 设置、读取方便，甚至页面刷新不丢失数据
- 容量较大，sessionStorage约5M，localStorage约20M
- 只能存储字符串，可以将对象JSON.stringfy()编码后存储

`web storage`和`cookie`的区别：

- `web storages`和`cookie`的作用不同，`web storage`是用于本地大容量存储数据(`web storage`的存储量大到5MB);而`cookie`是用于客户端和服务端间的信息传递；
- `web storage`有`setItem`、`getItem`、`removeItem`、`clear`等方法，`cookie`需要我们自己来封装`setCookie`、`getCookie`、`removeCookie`

### sessionStorage

- 生命周期为关闭浏览器窗口
- 在同一个窗口（页面）下数据可以共享
- 以键值对的形式存储使用

```js
// 存储数据
sessionStorage.setItem(key, value)
// 获取数据
sessionStorage.getItem(key)
// 删除数据
sessionStorage.removeItem(key)
// 删除所有数据
sessionStorage.clear()
```

### localStorage

- 生命周期为永久生效，除非主动删除否则关闭页面也会存在
- 可以多窗口（页面）共享（同一浏览器可以共享）
- 以键值对的形式存储使用

```js
// 存储数据
localStorage.setItem(key, value)
// 获取数据
localStorage.getItem(key)
// 删除数据
localStorage.removeItem(key)
// 删除所有数据
localStorage.clear()
```

### cookies

用于服务端和客户端的信息传递

`HTTP Cookie`（也叫Web Cookie或浏览器Cookie）是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。

`Cookie`主要用于以下三个方面：

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- 个性化设置（如用户自定义设置、主题等）
- 浏览器行为跟踪（如跟踪分析用户行为等）

cookie在一个浏览器下同一个域名下是全部共享一致的，和页签没任何关系，Cookie的作用域仅仅由domain和path决定，与协议和端口无关。

![库克展示](https://images-sally.oss-cn-beijing.aliyuncs.com/img/cookie%E5%B1%95%E7%A4%BA.png)

The **Cookies** table contains the following fields:

- **Name**. The cookie's name.
- **Value**. The cookie's value.
- **Domain**. The hosts that are allowed to receive the cookie.
- **Path**. The URL that must exist in the requested URL in order to send the `Cookie` header.
- **Expires / Max-Age**. The cookie's expiration date or maximum age. For session cookies this value is always `Session`.若无设置，在关闭浏览器时
- **Size**. The cookie's size, in bytes.

### indexDB

https://juejin.cn/post/6968769791691587615

https://www.ruanyifeng.com/blog/2018/07/indexeddb.html

https://zh.javascript.info/indexeddb#shi-wu

在用户的浏览器内持久化存储数据，是一个浏览器内建的数据库。IndexedDB 具有以下特点：

- **键值对储存**：IndexedDB 内部采用对象仓库（object store）存放数据。所有类型的数据都可以直接存入，包括 JavaScript 对象。对象仓库中，数据以"键值对"的形式保存，每一个数据记录都有对应的主键，主键是独一无二的，不能有重复，否则会抛出一个错误。
- **异步**：IndexedDB 操作时不会锁死浏览器，用户依然可以进行其他操作，这与 LocalStorage 形成对比，后者的操作是同步的。异步设计是为了防止大量数据的读写，拖慢网页的表现。
- **支持事务**：IndexedDB 支持事务（transaction），这意味着一系列操作步骤之中，只要有一步失败，整个事务就都取消，数据库回滚到事务发生之前的状态，不存在只改写一部分数据的情况。
- **同源限制：** IndexedDB 受到同源限制，每一个数据库对应创建它的域名。网页只能访问自身域名下的数据库，而不能访问跨域的数据库。
- **储存空间大**：IndexedDB 的储存空间比 LocalStorage 大得多，一般来说不少于 250MB，甚至没有上限。
- **支持二进制储存**：IndexedDB 不仅可以储存字符串，还可以储存二进制数据（ArrayBuffer 对象和 Blob 对象）。

1、打开数据库

```javascript
let request = window.indexedDB.open(name, version);
```

调用之后会返回 `request` 对象，我们需要监听该对象上的事件：

- `success`：数据库准备就绪，`openRequest.result` 中有了一个数据库对象“Database Object”，使用它进行进一步的调用。

  ```javascript
  // 成功打开数据库时回调
  request.onsuccess = () => {
    this.db = request.result;
  }
  ```

- `error`：打开失败。

- `upgradeneeded`：连接数据库时，版本号大于现有版本号或第一次创建数据库时也会回调，进行回调，可以利用版本号来更改数据结构

2、创建对象仓库，相当于建表 `createObjectStore`

```javascript
request.onupgradeneeded = function (event) {
  this.db = event.target.result
  var objectStore
  // 如果没有聊天 chat 表（对象仓库），则创建 chat
  if (!this.db.objectStoreNames.contains(that.tb_chat)) {
    objectStore = this.db.createObjectStore('chat', { keyPath: 'id' }) // 创建表 chat，并指定 id 为主键
    objectStore.createIndex('id', 'id', { unique: true }) // 创建 id 索引，具有唯一性
    objectStore.createIndex('uid', 'uid', { unique: false }) // 创建 发送者 索引
    objectStore.createIndex('uid2', 'uid2', { unique: false }) // 创建 接收者 索引
    objectStore.createIndex('sendTime', 'sendTime', { unique: true }) // 发送时间 索引
    objectStore.createIndex('uid_uid2', ['uid', 'uid2'], { unique: false }) // 发送者—接收者 索引
  }
}
```

`createObjectStore`主键有 keyPath，新增数据时，会自动给数据添加 id 属性，主键没有 keyPath，主键将只有值的概念，不会给数据添加主键信息：

```js
objectStore = this.db.createObjectStore('chat', { keyPath: 'id', autoIncrement: true }) // 在创建表时，添加 autoIncrement
objectStore = this.db.createObjectStore('chat', { autoIncrement: true }) // 在创建表时，添加 autoIncrement
```

`IDBObject.createIndex()`的三个参数分别为索引名称、索引所在的属性、配置对象（说明该属性是否包含重复的值）

3、事务

在 IndexedDB 里，增删改查都要通过事务来进行操作。如果中途有一个操作出现了异常，那么之前的操作都会进行回滚，数据回到操作前的样子。

```javascript
db.transaction(store[, type]);
```

- `store` 是事务要访问的库名称，例如 `"books"`。如果我们要访问多个库，则是库名称的数组。
- type – 事务类型，以下类型之一：
  - `readonly` —— 只读，默认值。
  - `readwrite` —— 只能读取和写入数据，而不能 创建/删除/更改 对象库。

4、增删改

对象库支持两种存储值的方法：

- **put(value, [key])** 将 `value` 添加到存储区。仅当对象库没有 `keyPath` 或 `autoIncrement` 时，才提供 `key`。如果已经存在具有相同键的值，则将替换该值。
- **add(value, [key])** 与 `put` 相同，但是如果已经有一个值具有相同的键，则请求失败，并生成一个名为 `"ConstraInterror"` 的错误。

```javascript
// 新增数据（返回主键）,tb是表名，data是要插入的数据
add(tb, data) {
  return new Promise((resolve, reject) => {
    var request = this.db.transaction([tb], 'readwrite').objectStore(tb).add(data)
    request.onsuccess = (event) => {
      resolve(event.target.result)
    }
    request.onerror = (event) => {
      reject(event)
    }
  })
}

// 根据主键删除数据
delete(tb, key) {
  return new Promise((resolve, reject) => {
    var request = this.db.transaction([tb], 'readwrite').objectStore(tb).delete(key)
    request.onsuccess = (event) => {
      resolve(event)
    }
    request.onerror = (error) => {
      reject(error)
    }
  })
}

// 根据主键修改数据
update(tb, data) {
  return new Promise((resolve, reject) => {
    var request = this.db.transaction([tb], 'readwrite').objectStore(tb).put(data)
    request.onsuccess = (event) => {
      resolve(event)
    }
    request.onerror = (error) => {
      reject(error)
    }
  })
}
```

5、查询

查询一条数据

```javascript
// 根据主键查询数据，key是主键的值
selectById(tb, key) {
  return new Promise((resolve, reject) => {
    var request = this.db.transaction([tb])
    .objectStore(tb)
    .get(key)

    request.onsuccess = (event) => {
      if (request.result) {
        resolve(request.result)
      } else {
        resolve()
      }
    }

    request.onerror = (error) => {
      reject(error)
    }
  })
}
// 根据索引查询，index代表索引字段，content代表索引的值，如果index的不是唯一的，也只能返回第一个搜索到的数据
select(tb, index, content) {
  return new Promise((resolve, reject) => {
    var request = this.db.transaction([tb])
    .objectStore(tb)
    .index(index)
    .get(content)

    request.onsuccess = (event) => {
      if (request.result) {
        resolve(request.result)
      } else {
        resolve()
      }
    }

    request.onerror = (error) => {
      reject(error)
    }
  })
}
```

6、游标查询，查询多条数据

```javascript
let cursor = store.openCursor(query, [direction]);
```

`query`是一个键或键范围，也可使用以下调用函数创建范围：

- `IDBKeyRange.lowerBound(lower, [open])` 表示：`≥lower`（如果 `open` 是 true，表示 `>lower`）
- `IDBKeyRange.upperBound(upper, [open])` 表示：`≤upper`（如果 `open` 是 true，表示 `<upper`）
- `IDBKeyRange.bound(lower, upper, [lowerOpen], [upperOpen])` 表示: 在 `lower` 和 `upper` 之间。如果 open 为 true，则相应的键不包括在范围中。
- `IDBKeyRange.only(key)` —— 仅包含一个键的范围 `key`

`direction` 是一个可选参数，使用顺序是：

- `"next"` —— 默认值，光标从有最小索引的记录向上移动。
- `"prev"` —— 相反的顺序：从有最大的索引的记录开始下降。
- `"nextunique"`，`"prevunique"` —— 同上，但是跳过键相同的记录 （仅适用于索引上的光标，例如，对于价格为 5 的书，仅返回第一本）。

主要的光标方法有：

- `cursor.advance(count)` —— 将光标向前移动 `count` 次，跳过值。
- `cursor.continue([key])` —— 将光标移至匹配范围中的下一个值（如果给定键，紧接键之后）

```javascript
let request = books.openCursor();

// 为光标找到的每本书调用
request.onsuccess = function() {
  let cursor = request.result;
  if (cursor) {
    let key = cursor.key; // 书的键（id字段）
    let value = cursor.value; // 书本对象
    console.log(key, value);
    cursor.continue();
  } else {
    console.log("No more books");
  }
};
```

### ISO 七层模型

OSI （Open System Interconnect）模型全称为开放式通信系统互连参考模型

ISO 七层模型: 物理层, 数据链路层, 网络层, 传输层, 会话层, 表示层, 应用层。 http对应应用层

- 应用层：通过应用程序间的交互来完成特定的网络应用
- 表示层：使通信的应用程序能够解释交换数据的含义。主要包括数据压缩，数据加密以及数据描述，使应用程序不必担心在各台计算机中表示和存储的内部格式差异
- 会话层：负责建立、管理和终止表示层实体之间的通信会话。提供数据交换的定界和同步功能，包括建立检查点和恢复方案的方法
- 传输层：为两台主机进程之间的通信提供服务，处理数据包错误、数据包次序，以及其他一些关键传输问题。传输层向高层屏蔽了下层数据通信的细节。主要的传输层协议是`TCP`和`UDP`
- 网络层：选择合适的网间路由和交换节点，确保数据按时成功传送。在发送数据时，网络层把传输层产生的报文或用户数据报封装成分组和包，向下传输到数据链路层。在网络层使用的协议是无连接的网际协议（Internet Protocol）和许多路由协议，因此我们通常把该层简单地称为 IP 层
- 数据链路层：将网络层交下来的 `IP`数据报组装成帧，在两个相邻节点间的链路上传送帧。每一帧的数据可以分成：报头head和数据data两部分: head 标明数据发送者、接受者、数据类型，如 MAC地址；data 存储了计算机之间交互的数据
- 物理层：实现计算机节点之间比特流的透明传送。主要任务是确定与传输媒体的接口的一些特性（机械特性、电气特性、功能特性，过程特性）。主要是和硬件有关

| 协议     | 处于层级 |
| -------- | -------- |
| HTTP协议 | 应用层   |
| TCP协议  | 传输层   |
| IP协议   | 网络层   |

## 重排/重绘

https://segmentfault.com/a/1190000039679970

浏览器渲染页面时，在获取完html、css资源之后，会大致经过以下步骤。
(1) html生成html树
(2) css形成css规则
(3) 两者形成一个渲染树
(4) 去文档当中找寻各自的布局位置，计算位置调整布局----- 排列
(5) 将内容填充到文档上 ----- 绘制

重排和位置的移动布局的变化有关，主要有以下几种情况会引发重排：
(1) 浏览器的窗口发生变化，每放大、缩小一次浏览器的窗口，该页面的所有元素都要进行重排重绘
(2) 增加、删除、移动dom元素，更改dom元素的宽高内外边距、内容，修改dom元素的样式
(3) 进行dom元素宽高等属性的查询，因为每查询一次，浏览器都会对所有的元素进行重新计算，以确保计算的值是正确的

重绘主要是元素的外观发生变化，不会重新布局，有以下情况会引发重绘：元素的背景(background)、文字颜色(color)、边框样式(outline)发生变化。

因为重排和重绘不只是对单个的dom元素进行操作，而是对整个【图层】进行操作，需要花费时间，如果频率高，非常的影响性能。

**减少重排/重绘**：减少了渲染进程的主线程和非主线程的很多计算和操作，能加快web的展示

1. 避免使用table布局

2. 使用class集中改中样式，而不是使用style单独改变

3. 缓存布局信息，分离读写操作

   ```javascript
   // bad 强制刷新 触发两次重排
   div.style.left = div.offsetLeft + 1 + 'px';
   div.style.top = div.offsetTop + 1 + 'px';
   
   // good 缓存布局信息 相当于读写分离
   var curLeft = div.offsetLeft;
   var curTop = div.offsetTop;
   div.style.left = curLeft + 1 + 'px';
   div.style.top = curTop + 1 + 'px';
   ```

4. 使用display none离线改变dom，然后再将元素展示。因为不可见的元素不会触发重排重绘

5. Debounce window resize 事件

6. will-change: transform 做优化

7. 对元素进行移动时，使用transform替代对元素top、left、right的操作，因为css3的整个操作是对图层的组合来实现的，所以不会引发重绘重排。

8. 使用absolute或者fixed，使元素脱离文档流，这样他们发生变化就不会影响其他元素

9. 避免频繁操作DOM，可以创建一个文档片段`documentFragment`，在它上面应用所有DOM操作，最后再把它添加到文档中


## 浏览器缓存

DNS缓存和页面资源缓存

https://juejin.cn/post/6844903593275817998

Web 缓存是指一个 Web 资源存在于 Web 服务器和客户端（浏览器）之间的副本。

好处是：减少网络延迟、降低服务器压力

缓存分为：强制缓存和协商缓存

强制缓存优先于协商缓存进行，若强制缓存(Expires和Cache-Control)生效则直接使用缓存，若不生效则进行协商缓存(Last-Modified / If-Modified-Since和Etag / If-None-Match)，协商缓存由服务器决定是否使用缓存，若协商缓存失效，那么代表该请求的缓存失效，重新获取请求结果，再存入浏览器缓存中；生效则返回304，继续使用缓存。

Expires ：指定⼀个缓存的过期时间，如果当次请求的资源在该过期时间之前，则命中缓存。缺点是 因为这个时间是⼀个绝对时间，所以当客户端本地时间被修改后，服务器与客户端时间偏差变⼤会 导致缓存混乱。

`Cache-Control`是最重要的规则, 用于控制网页缓存, 取值如下:

- `public`: 所有内容都被缓存(客户端和代理服务器都可缓存)
- `private`(默认值): 所有内容只有客户端可以缓存, Cache-Control
- `no-cache`: 字面意思是不要缓存, 实际上的机制是, 仍然对资源使用缓存, 但是每一次在使用缓存之前必须(MUST)想服务器对缓存资源进行验证. 也就是进行协商缓存
- `no-store`：所有内容都不会被缓存，即不使用强制缓存，也不使用协商缓存.
- `max-age=xxx` (xxx is numeric)：缓存内容将在 xxx 秒后失效, 缓存资源如果时间比指定的时间数值小, 那么客户端就直接接受花村的资源. 如果指定为0, 那么通常需要直接请求服务器
- `min-fresh=60(单位: 秒)`：要求缓存服务器返回至少还未过指定时间的缓存资源. 比如这里就是要求60秒内不会过期的资源返回。
- `s-maxage`（单位为 s)：同 max-age 作用一样，**只在代理服务器中生效**（比如 CDN 缓存）。比如当 `s-maxage=60 `时，在这 60 秒中，即使更新了`CDN`的内容，浏览器也不会进行请求。`max-age` 用于普通缓存，而 `s-maxage` 用于代理缓存。`s-maxage` 的优先级高于 max-age。如果存在 s-maxage，则会覆盖掉`max-age`和 `Expires header`。
- `must-revalidate`: 如果配置了`max-age`信息, 当资源小于max-age的时候使用使用缓存, 否则需要对资源进行验证.
- `max-stale=3600(单位: 秒)`: 可指示缓存资源, 即便是过期也照常接收.
- `only-if-cached`: 表示客户端仅在仅在缓存服务器本地缓存目标资源的情况下才会要求其返回. 换句话说, 该指令要求缓存服务器不重新加载响应, 也不会重新确认资源的有效性.
- `proxy-revalidate`: 要求所有的缓存服务器在接收到客户端带有该指令的请求返回响应之前, 必须再次验证缓存的有效性
- `no-transform`: 规定无论是在请求还是响应中, 缓存都不能改变实体主体的媒体类型

`Last-Modified` 表示本地文件最后修改日期，浏览器会在request header加上`If-Modified-Since`（上次返回的`Last-Modified`的值），询问服务器在该日期后资源是否有更新，有更新的话就会将新的资源发送回来.

`Etag`就像一个指纹，资源变化都会导致`ETag`变化，跟最后修改时间没有关系，`ETag`可以保证每一个资源是唯一的。`If-None-Match`的header会将上次返回的`Etag`发送给服务器，询问该资源的`Etag`是否有更新，有变动就会发送新的资源回来

![浏览器缓存](https://images-sally.oss-cn-beijing.aliyuncs.com/img/%E6%B5%8F%E8%A7%88%E5%99%A8%E7%BC%93%E5%AD%98.png)

dns(Domain Name System)：域名系统，负责把域名和IP地址做一一映射关系

浏览器请求DNS返回域名对应的IP。浏览器还提供了DNS数据缓存服务。通常，如果URL没有特别指明端口号，则HTTP协议默认是80端口。

点击刷新按钮或者按 F5、按 Ctrl+F5 （强制刷新）、地址栏回车有什么区别？

- **点击刷新按钮或者按 F5：** 浏览器直接对本地的缓存文件过期，但是会带上If-Modifed-Since，If-None-Match，这就意味着服务器会对文件检查新鲜度，返回结果可能是 304，也有可能是 200。
- **用户按 Ctrl+F5（强制刷新）：** 浏览器不仅会对本地文件过期，而且不会带上 If-Modifed-Since，If-None-Match，相当于之前从来没有请求过，返回结果是 200。
- 地址栏回车： 浏览器发起请求，按照正常流程，本地检查是否过期，然后服务器检查新鲜度，最后返回内容。

## 常见浏览器内核

内核：

- Trident：IE 浏览器内核；
- Gecko：Firefox 浏览器内核；
- Presto：Opera 浏览器内核；
- Webkit：Safari 浏览器内核；
- Blink：谷歌浏览器内核，属于 Webkit 的一个分支，与 Opera 一起在研发；

浏览器：

- IE：Trident，IE 内核；
- Chrome：以前是 Webkit，现在是 Blink 内核；
- Firefox：Gecko 内核；
- Safari：Webkit 内核；
- Opera：一起是 Presto，现在是 Blink 内核；
- 360、猎豹浏览器内核：IE + Blink 双内核；
- 搜狗、遨游、QQ 浏览器内核：Trident（兼容模式）+ Webkit（高速模式）；
- 百度浏览器、世界之窗内核：IE 内核；
- 2345 浏览器：以前是 IE 内核，现在是 IE + Blink 双内核；
- UC 浏览器内核：Webkit + Trident；

## 浏览器渲染的过程

https://mp.weixin.qq.com/s/N2kA6P4BhZwXJOsTR0UXqw

![浏览器渲染的过程](https://images-sally.oss-cn-beijing.aliyuncs.com/img/%E6%B5%8F%E8%A7%88%E5%99%A8%E8%BF%9B%E7%A8%8B.png)

- 最顶层是浏览器进程，负责协调处理其他进程模块的任务。
- UI 进程负责控制地址栏、标签页等；
- 渲染进程控制标签页内网站的展示。
- 插件进程控制站点使用的任意插件，比如：Flash。
- GPU 进程单独处理来自不同应用发送的绘制请求。

**当在浏览器输入URL地址，浏览器的处理：**

1. 判断输入的地址是什么，可能是外部网址，也可能是浏览器本身的设置页
2. 开始导航：UI 线程启用网络去调取网站的信息。网络线程会负责联系目标主机并获取到信息
3. 读取响应：网络进程返回了来自网站的响应，分为header 和payload二部分
4. 查找渲染进程：响应数据检查完毕，网络线程通知 UI线程所有的数据准备完毕，UI线程会渲染渲染进程去开始渲染web页面
5. 提交导航：数据和渲染进程准备就绪，浏览器进程会发送一个IPC到渲染进程提交导航，从而地址栏、标签页历史记录、前进/后退按钮等更新

**页面如何渲染：**

渲染进程内部包含主线程、工作线程、合成线程和光栅线程

1. 首先解析收到的文档，根据文档定义构建一颗 DOM 树，DOM 树是由 DOM 元素及属性节点组成的；
2. 然后对 CSS 进行解析，生成 CSSOM 规则树；
3. 根据 DOM 树和 CSSOM 规则树构建 Render Tree。渲染树的节点被称为渲染对象，渲染对象是一个包含有颜色和大小等属性的矩形，渲染对象和 DOM 对象相对应，但这种对应关系不是一对一的，不可见的 DOM 元素不会被插入渲染树。
4. 当渲染对象被创建并添加到树中，它们并没有位置和大小，所以当浏览器生成渲染树以后，就会根据渲染树来进行布局（也可以叫做回流）。这一阶段浏览器要做的事情就是要弄清楚各个节点在页面中的确切位置和大小。通常这一行为也被称为“自动重排”。
5. 布局阶段结束后是绘制阶段，比那里渲染树并调用对象的 paint 方法将它们的内容显示在屏幕上，绘制使用 UI 基础组件。

为了更好的用户体验，渲染引擎会尽可能早的将内容呈现到屏幕上，并不会等到所有的 html 解析完成之后再去构建和布局 render tree。它是解析完一部分内容就显示一部分内容，同时可能还在网络下载其余内容。

## 垃圾回收

第一步是标记空间中活动对象和非活动对象。所谓活动对象就是还在使用的对象，非活动对象就是可以进行垃圾回收的对象。

第二步是回收非活动对象所占据的内存。其实就是在所有的标记完成之后，统一清理内存中所有被标记为可回收的对象。

第三步是做内存整理。

### 回收机制

- JavaScript中存在两种变量：局部变量和全局变量。全局变量的生命周期会持续要页面卸载；而局部变量声明在函数中，它的生命周期从函数执行开始，直到函数执行结束，在这个过程中，局部变量会在堆或栈中存储它们的值，当函数执行结束后，这些局部变量不再被使用，它们所占有的空间就会被释放。
- 不过，当局部变量被外部函数使用时，其中一种情况就是闭包，在函数执行结束后，函数外部的变量依然指向函数内部的局部变量，此时局部变量依然在被使用，所以不会回收。

### 栈垃圾回收

记录当前执行状态的指针（称为 ESP)。当一个函数执行结束之后，JavaScript 引擎会通过向下移动 ESP 来销毁该函数保存在栈中的执行上下文，从而销毁栈中的垃圾

### 堆垃圾回收

在 V8 中会把堆分为新生代和老生代两个区域，新生代中存放的是生存时间短的对象，老生代中存放的生存时间久的对象。

新生区通常只支持 1～8M 的容量，而老生区容量大。

==副垃圾回收器==，主要负责新生代的垃圾回收。

用 Scavenge 算法来处理。把新生代空间对半划分为两个区域，一半是对象区域，一半是空闲区域。

新加入的对象都会存放到对象区域，当对象区域快被写满时，副垃圾回收器会把这些存活的对象复制到空闲区域中，同时它还会把这些对象有序地排列起来，消除内存碎片。完成复制后，对象区域与空闲区域进行角色翻转。

经过两次垃圾回收依然还存活的对象，会被移动到老生区中

==主垃圾回收器==，主要负责老生代的垃圾回收。

除了新生区中晋升的对象，一些大的对象会直接被分配到老生区。因此老生区中的对象有两个特点，一个是对象占用空间大，另一个是对象存活时间长。

- 标记 - 清除（Mark-Sweep）：从一组根元素开始，递归遍历这组根元素，在这个遍历过程中，调用栈能找到的元素称为活动对象，没有到达的元素就可以判断为垃圾数据。然后清楚掉标记为垃圾数据的对象（会产生内存碎片）
- 标记 - 整理（Mark-Compact）：标记后，让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存。
- 增量标记（Incremental Marking）：为了降低老生代的垃圾回收而造成的卡顿，V8 将标记过程分为一个个的子标记过程，同时让垃圾回收标记和 JavaScript 应用逻辑交替进行，直到标记阶段完成

### 导致内存泄漏的情况

- **意外的全局变量：** 由于使用未声明的变量，而意外的创建了一个全局变量，而使这个变量一直留在内存中无法被回收。
- **被遗忘的计时器或回调函数：** 设置了 setInterval 定时器，而忘记取消它，如果循环函数有对外部变量的引用的话，那么这个变量会被一直留在内存中，而无法被回收。
- **脱离 DOM 的引用：** 获取一个 DOM 元素的引用，而后面这个元素被删除，由于一直保留了对这个元素的引用，所以它也无法被回收。
- **闭包：** 不合理的使用闭包，从而导致某些变量一直被留在内存当中。

## console

### console.trace

输出当前的函数调用关系

### console.time

Starts a new timer. Call `console.timeEnd([label])` to stop the timer and print the elapsed time to the Console.

```javascript
 console.time();
for (var i = 0; i < 100000; i++) {
  let square = i ** 2;
}
console.timeEnd();
```

## Memory

用于分析内存中的对象

一共有三种内存分析工具：

- Snapshot：某个时间点的堆内存**快照**
- TimeLine：实时的按照**时间线**显示的内存分配情况
- Sampling：**采样**的方式收集内存分配情况

![图片](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-11-640-94399ed8a217a253f4d13ea217e7efe6-359)

## 浏览器分辨率兼容

1. 使用css3 @media
2. js判断浏览器类型  `window.navigator.userAgent`

## 浏览器安全

1. 同源策略

2. 跨站脚本攻击 xss(Cross-Site Scripting, XSS)：黑客往 HTML 文件或者DOM 注入恶意脚本，从而在用户浏览页面时利用注入的恶意脚本对用户实施攻击

   1. 类型
      1. 窃取cookie信息，`document.cookie`
      2. 监听用户行为，`addEventlistener`
      3. 修改dom构造假的登录页面
      4. 生成浮窗广告
   2. 如何阻止
      1. 纯前端，不使用服务端渲染。不用后端返回拼接实现页面
      2. 使用 CSP ，本质是建立一个白名单，告诉浏览器哪些外部资源可以加载和执行

3. CSRF（Cross-Site Request Forgeries，CSRF） 攻击指的是**跨站请求伪造攻击**，攻击者诱导用户进入一个第三方网站，然后该网站向被攻击网站发送跨站请求。如果用户在被攻击网站中保存了登录状态，那么攻击者就可以利用这个登录状态，绕过后台的用户验证，冒充用户向服务器执行一些操作。

   CSRF 攻击的**本质是利用 cookie 会在同源请求中携带发送给服务器的特点，以此来实现用户的冒充。**

   解决办法：进行同源检测，使用 CSRF Token 进行验证，

4. iframe的滥⽤: iframe中的内容是由第三⽅来提供的，默认情况下他们不受控制，他们可以在iframe中运⾏JavaScirpt脚本、Flash插件、弹出对话框等等，这可能会破坏前端⽤户体验；

## js影响DOM解析

解析 HTML、下载 CSS、下载 JavaScript、生成 CSSOM、执行 JavaScript、生成布局树、绘制页面

```html

<html>
<body>
    <div>1</div>
  	<!-- 1. 插入js文件 -->
    <!--  <script type="text/javascript" src='foo.js'></script> -->
  	<!-- 2. 插入js脚本 -->
    <script>let div1 = document.getElementsByTagName('div')[0]    div1.innerText = 'time.geekbang'</script>
    <div>test</div>
</body>
</html>
```

1、以上2种方式都会阻塞DOM解析。插入文件方式需要先下载文件，然后解析。下载的过程也阻塞。

2、如果 JavaScript 文件中没有操作 DOM 相关代码，可将该 JavaScript 脚本设置为异步加载。外联脚本才支持以下规则。

https://blog.csdn.net/liuhe688/article/details/51247484

`defer`：用于开启新的线程下载脚本文件，在 DOMContentLoaded 事件之前执行。

`async`：HTML5新增属性，用于异步下载脚本文件，下载完毕立即解释执行代码。会在`load`事件之前执行

```html
<script async type="text/javascript" src='foo.js'></script>
<script defer type="text/javascript" src='foo.js'></script>
```

![img](https://images-sally.oss-cn-beijing.aliyuncs.com/images/2022-01-15-20160503104416135-284aec5bb7f16b3ef4e7482110c5ddbb-a15.jpeg)

3、渲染引擎在遇到 JavaScript 脚本时，不管该脚本是否操纵了 CSSOM，都会先执行 CSS 文件下载，解析操作，再执行 JavaScript 脚本。因为JS脚本中可能涉及到操纵CSSDOM。这两个文件的下载过程是重叠的，所以下载时间按照最久的那个文件来算。







浏览器进程主要负责用户交互、子进程管理和文件储存等功能。

网络进程是面向渲染进程和浏览器进程等提供网络下载功能。

渲染进程的主要职责是把从网络下载的 HTML、JavaScript、CSS、图片等资源解析为可以显示和交互的页面。因为渲染进程所有的内容都是通过网络获取的，会存在一些恶意代码利用浏览器漏洞对系统进行攻击，所以运行在渲染进程里面的代码是不被信任的。这也是为什么 Chrome 会让渲染进程运行在安全沙箱里，就是为了保证系统的安全。



UserAgent 样式，它是浏览器提供的一组默认样式，如果你不提供任何样式，默认使用的就是 UserAgent 样式。

队列限定只能在队尾(rear)插入元素，队头(fromt)删除元素。队列是先进先出线性表。

栈只能在栈顶(top)进行插入和删除操作，另一段称为栈底(bottom)。栈是后进先出线性表。


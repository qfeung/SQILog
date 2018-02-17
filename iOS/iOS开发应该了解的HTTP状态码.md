# iOS开发应该了解的HTTP状态码

## Category (分类)

	| 类别 | 含义
--- | --- | ---
1XX | Informational (信息性状态码) | 接收的请求正在处理
2XX | Success (成功状态码) | 请求正常处理完毕
3XX | Redirection (重定向状态码) | 需要进行附加操作以完成请求
4XX | Client Error (客户端错误状态码) | 服务器无法处理请求
5XX | Server Error (服务器错误状态码) | 服务器处理请求出错

---

## Detail (详解)

### 2XX 成功

#### 200 OK

服务端成功处理了客户端的请求, 没有发现异常.

#### 204 No Content

服务端成功处理了客户端的请求, 但是没有资源可以返回.

#### 206 Partial Content

服务器成功处理了客户端的范围请求.

### 3XX 重定向

#### 301 Moved Permanently

永久性重定向. 表示请求的资源已被分配了新的URI, 以后应使用资源现有的URI.

#### 302 Found

临时性重定向. 表示请求的资源已被分配了新的URI, 希望用户(本次)能使用新的URI访问.

#### 303 See Other

303状态码和302状态码有着相同的功能, 但303状态码明确表示客户端应当采用GET方法获取资源.

#### 304 Not Modified

1. 客户端发送GET请求获取资源时, 报文中包含`If-Match`, `If-Modified-Since`, `If-None-Match`, `If-Range`, `If-Unmodified-Since`中任一首部.
2. 服务端允许资源访问, 但是由于请求报文中的首部条件未满足, 直接返回304 Not Modified (服务端资源未改变, 可直接只用客户端未过期的缓存).
2. 虽然被划分为3XX, 但是和重定向没有关系.

#### 307 Temporary Redirect

临时重定向.

### 4XX 客户端错误

表示客户端是发生错误的原因所在.

#### 400 Bad Request

请求报文中存在语法错误.

#### 401 Unauthorized

表示:发送的请求需要有通过HTTP认证的认证信息; 若之前已经进行过1次请求, 则表示用户认证失败.

#### 403 Forbidden

资源请求被服务器拒绝了.

#### 404 Not Found

无法找到请求的资源.

### 5XX 服务器错误

#### 500 Internal Server Error

服务器在执行请求时发生了错误.

#### 503 Service Unavailable

表明服务器暂时处于超负荷或者正在进行停机维护, 现在无法处理请求.
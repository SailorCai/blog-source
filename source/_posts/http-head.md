---
title: HTTP1.1头部字段汇总
date: 2018-09-16 23:04:04
comment: true
tags: [前端,http]
categories: [前端,http]
---
### HTTP/1.1是目前HTTP协议使用最广泛的版本
HTTP首部字段是构成HTTP报文的要素之一。在客户端和服务器的交互中传递重要的交互信息。

**首先首部字段类型分为四种：**
**1、通用首部字段(General Header Feilds)**

即请求报文和响应报文双方都会使用的首部

**2、请求首部字段（Request Header Fields）**

从客户端向服务器发送请求时使用的首部。补充了请求的附加内容、客户端信息、响应内容想关优先级等信息。

**3、响应首部字段（Response Header Fields）**

从服务器向客户端返回响应时使用的首部。补充了相应的附加内容

**4、实体首部字段（Entity Header Fields）**

针对请求报文和响应豹纹的实体部分使用的首部。补充了资源内容更新时间等与实体有关的信息

下图是在一次真实的请求中的头部信息：
<img src="http1.png" alt="">

**表1：通用首部字段**

|首部字段名|说明|
|------|------|
|Cache-Control| 控制缓存的行为，一般包括一个“max-age=3400”的值，代表缓存的有效时间为资源返回后的3400秒之内|
|Connection|逐跳首部、连接的管理。如果值为其他字段的名字，代表代理服务器转发时将不再携带该字段。如果值为close或Keep-Alive则分别表示连接为短/长连接|
|Date|创建报文的日期时间|
|Pragma|报文指令。是HTTP/1.1之前版本的遗留字段，仅作为与HTTP/1.0的向后兼容而定义|
|Trailer|报文末端的首部一览|
|Transfer-Encoding | 指定报文主体的传输编码方式|
|Upgrade|升级为其他协议|
|Via|代理服务器的相关信息。报文经过代理或网关时，会先在首部字段Via中附加该服务器的信息，然后再进行转发，目的是追踪请求和响应报文的传输路径|
|Warning|错误通知|

**表2：请求首部字段**

| 首部字段名 | 说明 |
| ------ | ------ |
| Accept | 用户代理可处理的媒体类型 |
| Accept-Charset | 优先的字符集 |
| Accept-Encoding | 优先的内容编码 |
| Authorization | Web认证信息 |
|Expect | 期待服务器的特定行为 |
| From | 用户的电子邮箱地址 |
| Host | 请求资源所在的服务器主机名/域名 |
| If-Match | 比较实体标记（Etag） |
| If-Modified-Since	比较资源的更新时间，出现在条件get请求中
| If-None-Match | 比较实体标记（与If-Match相反） |
| If-Range | 资源未更新时发送实体Byte的范围请求 |
| If-Unmodified-Since | 比较资源的更新时间（与If-Modified-Since相反） |
| Max-Forwards | 最大传输逐跳数 |
| Proxy-Authorization | 代理服务器要求客户端的认证信息 |
| Range | 实体的字节范围请求 |
| Referer | 请求中URI的原始获取方 |
| TE | 传输编码的优先级 |
| User-Agent | HTTP客户端程序的信息。如果是浏览器则是对应浏览器的用户代理字符串 |

**表3：响应首部字段**

| 首部字段名 | 说明 |
| ------ | ------ |
| Accept-Ranges | 是否接受字节范围请求 |
| Age | 推算资源创建经过时间 |
| ETag | 资源的匹配信息 |
| Location | 令客户端重定向至指定URI |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
| Server | HTTP服务器的安装信息 |
| Vary | 代理服务器缓存的管理信息 |
| WWW-Authenticate | 服务器对客户端的认证信息 |

**表4：实体首部字段**

| 首部字段名 | 说明 |
| ------ | ------ |
| Allow | 资源可支持的HTTP方法 |
| Content-Encoding | 实体主体适用的编码方式 |
| Content-language | 实体主体的自然语言 |
| Content-length | 实体主体的大小（单位：字节） |
| Content-MD5 | 实体主体的报文摘要 |
| Content-Range | 实体主体的位置范围 |
| Content-Type | 实体主体的媒体类型 |
| Expires | 实体主体过期的日期时间 |
| Last-Modified | 资源的最后修改日期时间 |


以上就是HTTP/1.1几乎所有的首部字段了，当然还有其他的比较重要的非HTTP/1.1首部字段，如Cookie、Set-Cookie等等

**文章内容参考了《图解HTTP》上野宣 著   于均良 译。**
---
title: HTTP协议（请求头部）
date: 2018-05-09 17:31:06
tags: [http,协议]
---
## HTTP 请求和响应报文模型

![http报文模型](\images\http\http-head-request.png)
![http报文模型](\images\http\http-head-response.png)


通常HTTP消息包括客户机向服务器的请求消息和服务器向客户机的响应消息。

这两种类型的消息由一个**起始行**，一个或者多个**头域**，一个只是头域结束的**空行**和可选的**消息体**组成。

**HTTP的头域**包括**通用头**，**请求头**，**响应头**和**实体头**四个部分。

每个**头域**由一个**域名**，**冒号**（:）和**域值**三部分组成。域名是大小写无关的，域值前可以添加任何数量的空格符，头域可以被扩展为多行，在每行开始处，使用至少一个空格或制表符。 

## 头域

### 通用头域

* Cache-Control头域

  ​	Cache-Control指定请求和响应遵循的缓存机制。在请求消息或响应消息中设置Cache-Control并不会修改另一个消息处理过程中的缓存处理过程。请求是缓存指令包括no-cache、no-store、max-age、max-stale、min-fresh、only-if-cached，响应消息中的指令包括public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age.

  1. Public指示响应可被任何缓存区缓存。 
  2. Private指示对于单个用户的整个或部分响应消息，不能被共享缓存处理。这允许服务器仅仅描述当用户的部分响应消息，此响应消息对于其他用户的请求无效。
  3. no-cache指示请求或响应消息不能缓存 
  4. no-store用于防止重要的信息被无意的发布。在请求消息中发送将使得请求和响应消息都不使用缓存。
  5. max-age指示客户机可以接收生存期不大于指定时间（以秒为单位）的响应。
  6. min-fresh指示客户机可以接收响应时间小于当前时间加上指定时间的响应。
  7. max-stale指示客户机可以接收超出超时期间的响应消息。如果指定max-stale消息的值，那么客户机可以接收超出超时期指定值之内的响应消息。

* Date头域

  ​

* Pragma头域

### 请求头域

### 响应头域

### 实体头域







**引用**:[https://blog.csdn.net/guoguo1980/article/details/2649658]
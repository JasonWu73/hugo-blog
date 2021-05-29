---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- http
series:
- HTTP
title: HTTP 请求与响应报文格式
date: 2021-05-30T03:47:01+08:00
description: HTTP 请求报文和 HTTP 响应报文格式。
---

> {{<reprint>}}

{{< param description >}}

## HTTP 请求

```http
GET /posts/1 HTTP/1.1
Host: jsonplaceholder.typicode.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36
Accept-Language: en,zh;q=0.9

<BODY>
```

请求报文（request message）格式：

1. 请求行（Request-Line）
    - `Request-Line = Method SP Request-URI SP HTTP-Version CRLF`
2. 0个或多个请求头
3. 空行，指示请求头字段结束
4. 一个可选的请求体

## HTTP 响应

```http
HTTP/1.1 200 OK
Date: Sat, 29 May 2021 19:09:13 GMT
Content-Type: application/json; charset=UTF-8

<BODY>
```

响应报文（response message）格式：

1.  状态行（Status-Line）
    - `Status-Line = HTTP-Version SP Status-Code SP Reason-Phrase CRLF`
2. 0个或多个响应头
3. 空行，指示响应头字段结束
4. 一个可选的响应体

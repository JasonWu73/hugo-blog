---
toc: true
categories:
  - "Network"
tags:
  - "http"
series:
  - "HTTP(S)"
title: "HTTP 请求与响应报文格式"
date: "2021-05-30"
description: HTTP 请求报文和 HTTP 响应报文格式
---

## 请求报文（Request Message）

```http
GET /posts/1 HTTP/1.1
Host: jsonplaceholder.typicode.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36
Accept-Language: en,zh;q=0.9

<BODY>
```

1. 请求行（Request-Line）
    - `Request-Line = Method SP Request-URI SP HTTP-Version CRLF`
2. 0个或多个请求头
3. 空行，指示请求头字段结束
4. 一个可选的请求体

## 响应报文（Response Message）

```http
HTTP/1.1 200 OK
Date: Sat, 29 May 2021 19:09:13 GMT
Content-Type: application/json; charset=UTF-8

<BODY>
```

1.  状态行（Status-Line）
    - `Status-Line = HTTP-Version SP Status-Code SP Reason-Phrase CRLF`
2. 0个或多个响应头
3. 空行，指示响应头字段结束
4. 一个可选的响应体

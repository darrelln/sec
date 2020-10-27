---
layout: default
title: HTTP
parent: COOKBOOKS
has_children: false
has_toc: false
---

{% include toc.html %}

## cURLing web requests
```
curl {-I | --head} http://.../                  // Head request
```
```
HTTP/1.1 200 OK
Content-Length: 32797
Content-Type: text/html
Last-Modified: Thu, 23 Jan 2020 17:14:44 GMT
Accept-Ranges: bytes
ETag: "4bdc4b9b10d2d51:0"
Server: Microsoft-IIS/10.0                      // Helpful
Date: Sat, 06 Jun 2020 17:22:23 GMT
```

<br />

```
curl -X TRACE http://10.10.10.175/
```
```
501 - Header values specify a method that is not implemented
```
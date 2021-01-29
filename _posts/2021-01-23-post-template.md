---
layout: post
title:  "抓包工具Charles使用说明"
date:   2021-02-09 17:34:02 +0800
categories: charles
---

### 前言
自从用了mac之后,抓包工具: wiresharks就没有用,需要寻找一个替代品,就用了charles来完成抓包工作.本文所述是在mac下完成

### charles相关
- 定义: Charles is an HTTP proxy / HTTP monitor / Reverse Proxy that enables a developer to view all of the HTTP and SSL / HTTPS traffic between their machine and the Internet. This includes requests, responses and the HTTP headers (which contain the cookies and caching information).
- 官网: [charles官网](https://www.charlesproxy.com/)
- 官方文档: [charles官方文档](https://www.charlesproxy.com/documentation/welcome/)

### charles或者抓包工具的原理
- 从定义得知charles是通过HTTP和SOCKS代理服务器实现的,一个网络终端(一般为客户端)通过代理服务器与另一个网络终端(一般为服务器)进行非直接的连接
- 官网Proxying说明:
	Charles is an HTTP and SOCKS proxy server. Proxying requests and responses enables Charles to inspect and change requests as they pass from the client to the server, and the response as it passes from the server to the client

### 相关代理名词解释
**代理(Proxy)**: 即前向代理.网络代理，是一种特殊的网络服务，允许一个网络终端（一般为客户端）通过这个服务与另一个网络终端（一般为服务器）进行非直接的连接。一些网关、路由器等网络设备具备网络代理功能。一般认为代理服务有利于保障网络终端的隐私或安全，防止攻击。


......


### 注意事项
- 使用Recording Settings抓取本地服务设置成localhost或者127.0.0.1抓取不到的原因,多半跟charles的实现原理有关,网络没有转发,抓取不到
......

### 思考
- 抓包的原理

### 参考资料
[Charles 功能介绍和使用教程](https://juejin.im/post/5b8350b96fb9a019d9246c4c)   
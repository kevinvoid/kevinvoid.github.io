---
layout: post
title:  "centos ssh 免密码登录"
date:   2020-09-20 08:329:02 +0800
categories: linux
---

### 前言
服务器之间的登录可通过密码登录和免密码登录,有密码登录即ssh root@ip,再输入密码.无密码登录就是ssh通过把客户端的公约保存在服务器的信任钥匙池中实现免密登录

### 实现步骤
- 
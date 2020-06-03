---
layout: post
title:  "Spring Boot搭建tls服务器,访问提示ERR_SSL_VERSION_OR_CIPHER_MISMATCH"
date:   2020-06-03 17:08:02 +0800
categories: java
---

### 前言
一直打算搭建一个spring boot tls服务器,就是http转https访问,最近得空搭建了一个,访问一直提示“ERR_SSL_VERSION_OR_CIPHER_MISMATCH”,在此记录下问题出现的原因及解决方案

### 搭建spring boot tls步骤
- idea利用Spring Initializr创建Spring boot项目
- 利用keytool命令创建密钥库jks文件(keytool命令在此不做详细介绍,可参考: [keytool命令](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html))
```
keytool -genkey -alias kevin-dsa -keystore 
/Users/apple/tool/keytool/kevin-dsa.jks -storetype pkcs12
```
- 拷贝kevin-dsa.jks文件到工程下resources目录下
- 配置application.yml文件(spring boot 2.3默认是application.propertes文件,可自行创建yml文件),配置信息如下:
```
server:
  ssl:
    enabled: true
    key-store: classpath:kevin-dsa.jks
    key-store-type: JKS

    key-alias: kevin-dsa
    key-store-password: password

  port: 8443
```
说明: **key-store-password**是生成jks文件时设置的密钥库密码

- 创建一个controller业务类验证是否能访问:  
```
	@Controller
	@RequestMapping("/api/monitor")
	public class MonitorController {

	    @GetMapping("/alive")
	    @ResponseBody
	    public String alive() {
	        // return Result.builder().result("success" + Math.random()).success().build();
	        return "success" + Math.random();
	    }

	}
```

- 启动项目
- chrome访问: [https://localhost:8443/api/monitor/alive](https://localhost:8443/api/monitor/alive),提示信息如下:
```
	此网站无法提供安全连接
	localhost 使用了不受支持的协议。
	ERR_SSL_VERSION_OR_CIPHER_MISMATCH

	详细信息
	协议不受支持
	客户端和服务器不支持一般 SSL 协议版本或加密套件。
```

### 解决方案及过程
- ERR_SSL_VERSION_OR_CIPHER_MISMATCH,提示ssl协议版本或者加密套件不一致,ssl是tls前身,其实是相同的技术.关于两者的区别在此不做讨论,下面介绍下tls;  
TLS: 传输层安全性协议（英语：Transport Layer Security，缩写：TLS）及其前身安全套接层（英语：Secure Sockets Layer，缩写：SSL）是一种安全协议，目的是为互联网通信提供安全及数据完整性保障.
原理在此不做叙述.  
&nbsp;&nbsp;&nbsp;&nbsp;
由此可见,TLS提供了一种加密手段来保证互联网数据传输过程的安全, 下面我们根据异常提示查看证书的加密

- 查看kevin-dsa.jks加密信息
	- 命令: keytool -list -v -keystore kevin-dsa.jks
	- output:  
```
密钥库类型: PKCS12
密钥库提供方: SUN
您的密钥库包含 1 个条目
别名: kevin-dsa
创建日期: 2020-6-2
条目类型: PrivateKeyEntry
证书链长度: 1
证书[1]:
所有者: CN=kevinvoid, OU=kevinvoid, O=kevinvoid clt, L=beijing, ST=beijing, C=CN
发布者: CN=kevinvoid, OU=kevinvoid, O=kevinvoid clt, L=beijing, ST=beijing, C=CN
序列号: 30f6a7d9
有效期为 Tue Jun 02 16:14:46 CST 2020 至 Mon Aug 31 16:14:46 CST 2020
证书指纹:
         MD5:  7E:90:5A:EF:3D:33:53:F6:B5:50:7E:29:0B:F2:DC:82
         SHA1: 9B:56:D2:49:B2:43:7F:7D:03:85:D8:36:80:50:27:53:83:E6:E8:5E
         SHA256: 1A:81:42:DC:6D:A7:65:6D:99:29:EF:79:84:79:A2:F7:16:11:00:E7:26:0B:7C:06:90:64:41:D2:6C:36:73:26
签名算法名称: SHA256withDSA
主体公共密钥算法: 2048 位 DSA 密钥
版本: 3
扩展: 
#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 53 3B F0 28 4F 5B 81 A3   B4 A4 31 95 37 77 13 1C  S;.(O[....1.7w..
0010: 12 3C 7B F2                                        .<..
]
]
*******************************************
*******************************************
```
	- 由output中的**主体公共密钥算法信息**可得知,此密钥库是使用DSA加密的
	- DSA: Digital Signature Algorithm, 数字签名算法,用于数字签名算法: DSA数字签名和认证中，发送者使用自己的私钥对文件或消息进行签名，接受者收到消息后使用发送者的公钥来验证签名的真实性.由此可见DSA算法不是用来加密的,非对称算法中可使用RSA加密,接下来通过生成RSA算法的jks文件来尝试解决问题

- 生成RSA密钥算法的jks文件
	- keytool 官网介绍: [keytool命令](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)
	- keytool -genkey -h 查看命令参数信息
```
➜  keytool keytool -genkey -h
keytool -genkeypair [OPTION]...
生成密钥对
选项:
 -alias <alias>          要处理的条目的别名
 -keyalg <alg>           密钥算法名称
 -keysize <size>         密钥位大小
 -sigalg <alg>           签名算法名称
 -destalias <alias>      目标别名
 -dname <name>           唯一判别名
 -startdate <date>       证书有效期开始日期/时间
 -ext <value>            X.509 扩展
 -validity <days>        有效天数
 -keypass <arg>          密钥口令
 -keystore <keystore>    密钥库名称
 -storepass <arg>        密钥库口令
 -storetype <type>       密钥库类型
 -providername <name>    提供方名称
 -addprovider <name>     按名称 (例如 SunPKCS11) 添加安全提供方
   [-providerarg <arg>]    配置 -addprovider 的参数
 -providerclass <class>  按全限定类名添加安全提供方
   [-providerarg <arg>]    配置 -providerclass 的参数
 -providerpath <list>    提供方类路径
 -v                      详细输出
 -protected              通过受保护的机制的口令
使用 "keytool -help" 获取所有可用命令
```
	- 由上output输出可见,可通过指定-keyalg参数来指定密钥算法
```
keytool -genkey -alias kevin  -keyalg RSA -keystore 
/Users/apple/tool/keytool/kevin.jks -storetype pkcs12
```
	- 把生成kevin.jks复制到项目resources目录下,并修改相应的别名信息
	- 访问 [https://localhost:8443/api/monitor/alive](https://localhost:8443/api/monitor/alive)
	- 接口正常返回: success0.24189317987874737

### 说明
- 此种情况只是出现ERR_SSL_VERSION_OR_CIPHER_MISMATCH问题的原因之一
- keytool命令参数genkey已经替换成了genkeypair,具体哪个jdk版本替换的,可查询资料
- 代码路径(不对外开放):  https://github.com/kevinvoid/spring-boot-tls.git

### 思考
&nbsp;&nbsp;&nbsp;&nbsp;
对于一种技术,不仅仅要学会使用,更重要的是要知其然知其所以然,如此定位问题才能更准确


### 参考文献:
[keytool命令说明](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) 






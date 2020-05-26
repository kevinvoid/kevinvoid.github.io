---
layout: post
title:  "字符集、字符集编码及其在java和mysql设置"
date:   2020-05-26 15:06:02 +0800
categories: 字符集、字符集编码
---

### 前言
&nbsp;&nbsp;&nbsp;&nbsp;
计算机存储的信息的都是使用二进制来表示,我们日常看到的计算机输出的英文、汉字、符号、图片等都是通过二进制转换之后的结果.简单地说,一个字符‘a‘用什么表示,取决于其采用的字符集编码所决定.例如用ASCII字符集表示‘a’就是0110 0001.一个字符用什么表示,称之为“编码”.反之,将存储在计算机中二进制数解析出来称之为“解码”.不同的字符集所能表达的范围有限,最初的字符集是ASCII,有128个码位,用7位即可表示.如果只是用英文字符,ASCII是足够使用的.后来计算机使用越来越广,ASCII码位不够,于是在此基础上扩展,渐渐地扩展出了支持更多码位的字符集,如:单字节表示ISO-8859-1、双字节表示的GB2312等,想要表达更多的字符,必须扩展字符所占用的大小.  

&nbsp;&nbsp;&nbsp;&nbsp;常见的字符集有ASCII字符集、GB2312字符集、BIG5字符集、GB18030字符集、Unicode字符集等,一般每一种字符集对应一种字符集编码,unicode除外,unicode有多种字符集编码实现方式,如UTF-8、UTF-16.
    

### 名词解释
**字符**: 各种文字和符号的总称，包括各国家文字、标点符号、图形符号、数字等。java用一个字节(byte)存储一个字符(Character),也就是8位  
**字节**: 字节是一个8bit的存储单元，取值范围是0x00到0xFF。 根据字符编码的不同，一个字符可以是单个字节的，也可以是多个字节的  
**字符集**: 字符的集合就叫字符集。不同集合支持的字符范围不一样，譬如ASCII只支持英文，GB18030支持中文等等,ASCII是八位的,最大支持256个字符的表示  
**CCS**：Coded Character Set,即编码字符集，给字符表里的抽象字符编上一个数字，也就是字符集合到一个整数集合的映射。这种映射称为编码字符集，Unicode 字符集就是属于这一层的概念； 
**CEF**：Character Encoding Form, 即字符编码表，根据一定的算法，将编码字符集（CCS） 中字符对应的码点转换成一定长度的二进制序列，以便于计算机处理，这个对应关系被称为字符编码表，UTF-8、 UTF-16 属于这层概念；  
**字符编码**: 定义字符集中的字符如何编码为特定的二进制数，以便在计算机中存储。 字符集和字符编码一般一一对应  
**ASCII**:（American Standard Code for Information Interchange）美国标准信息交换码,用7位二进制表示一个字符,用于计算1个字节是8位,所以最高位是0,即00000000-01111111或0x00-0x7F。  
**Unicode**: 为了解决不同字符集码表不一致而推出的，统一了所有字符对应的码.UTF-8是Unicode字符集的一种字符编码实现方式，它规定了字符该如何编码成二进制，存储在计算机中  
**UTF**: Unicode Transformation Format,将Unicode编码进行了转换，通常会在存储空间和效率上进行一定的权衡，有很多种实现方式，UTF-8和UTF-16是最常用的  
**UTF-8**: 一种变长的编码方式，一般用1到4个字节序列来表示Unicode字符  

### 操作系统相关
- mac查看字符集命令: locale
```
 _posts git:(master) ✗ locale
LANG="zh_CN.UTF-8"
LC_COLLATE="zh_CN.UTF-8"
LC_CTYPE="zh_CN.UTF-8"
LC_MESSAGES="zh_CN.UTF-8"
LC_MONETARY="zh_CN.UTF-8"
LC_NUMERIC="zh_CN.UTF-8"
LC_TIME="zh_CN.UTF-8"
LC_ALL=
```
- 查看文件的字符集编码: file命令
```
file 2020-05-17-java程序启动虚拟机参数.md
2020-05-17-java程序启动虚拟机参数.md: UTF-8 Unicode text
```

### java web字符集设置:
- java默认字符集设定  
	Java程序默认字符集的优先级顺序
	1. 程序执行时使用 Properties 指定的字符集，如 System.getProperties().put("file.encoding","UTF-8);
	2. 参数-Dfile.encoding 指定的字符集，如 Windows 可以通过设置环境变量 JAVA_TOOL_OPTIONS=-Dfile.encoding=UTF-8
	3. 操作系统默认的字符集；
	4. JDK 中 JVM 默认的字符集。

- JSP：  
通过JSP页面命令<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>设置 JSP 	页面的编码方式（pageEncoding）以及提交表单时所使用的编码方式（charset 属性）；

- HTML:   
设置 Content-Type 属性<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">；
- Servlet：  
设置 response.setContentType("text/html;charset=UTF-8")，即服务器端编码方式；
- POST请求：  
通过request.setCharacterEncoding ("UTF-8")设置解码方式、response.setCharacterEncoding("UTF-8")设置服务器端响应数据的编码方式
```
protected void service(HttpServletRequest request, HttpServletResponse
response) throws ServletException, IOException {
// TODO Auto-generated method stub
request.setCharacterEncoding("UTF-8");
response.setCharacterEncoding("UTF-8");
}
```
- Get请求：  
Get 请求方式中请求参数会被附加到地址栏的 URL 之后，URL 组成：域名:端口/contextPath/servletPath/pathInfo?queryString，不同的服务器对 URL 中 pathInfo 和 queryString 解码方式设定不一样，比如 Tomcat 服务器一般在 server.xml 中设定的，Tomcat8 之前的版本默认使用的是 ISO-8859-1，但是 Tomcat 8 默认使用的是 UTF-8，如清单 4 所示；另外对于双字节字符如中日韩等作为 URL 的参数时，最好先 URI 编码后在放到 URL 中 URLEncoder.encode(String.valueOf(c),"UTF-8")；

- XML文档：  
设置 transformer.setOutputProperty(OutputKeys.ENCODING, "UTF-8");

- 调用 API 时指定字符集：  
调用字符串操作相关或者其他 I/O 操作 API 时，最好指定字符集，如 String.getBytes("UTF-8")，而不是 String.getBytes()；根据情况必要的时候需要写入BOM头，例如 Microsoft Office 在读取 csv 文件的时候，它将首先读取 BOM，然后使用 BOM 中的编码打开文件，这种情况下我们在创建或者导出到 CSV 文件的时候，我们需要在代码里面写入BOM头
```
private static void createCsvFile() {
try {
    FileOutputStream fos = new FileOutputStream("c:\\test.csv");
    String str ="姓名,性别,年龄,出生年月";
	fos.write(new byte[]{(byte)0xEF,(byte)0xBB, (byte)0xBF}); 
    OutputStreamWriter osw = new OutputStreamWriter(fos, "UTF-8"); 
    BufferedWriter b_writer = new BufferedWriter(osw); 
    b_writer.write(str); 
    b_writer.close(); 
} catch (IOException e) { 
    System.out.println(e.toString()); 
}
}
```

### java相关的
- 查看java所使用的字符集编码:
```
Properties initProp = new Properties(System.getProperties());
System.out.println("当前系统编码:" + initProp.getProperty("file.encoding"));
```
- 查看运行中java程序字符集编码: 可通过jinfo命令打印系统所有的参数
```
jinfo -sysprops 55827
```
- 修改java字符编码: 启动java程序可通过添加-Dfile.encoding=UTF-8指定字符集编码
- java.lang.String.getBytes方法
- 示例
	- 编码方式设置成ASCII,ASCII显示不了中文
		- 设置-Dfile.encoding=ASCII
		- 代码:
		```
		System.out.println("123abc北京");
		```
		- 输出
		```
		123abc??
		```
	- 字符编码方式转换
	- 文件编码不同的转换

### mysql相关
- 查看mysql支持的字符集及字符集序: SHOW CHARACTER SET;
```
utf16	UTF-16 Unicode	utf16_general_ci	4
utf16le	UTF-16LE Unicode	utf16le_general_ci	4
utf32	UTF-32 Unicode	utf32_general_ci	4
utf8	UTF-8 Unicode	utf8_general_ci	3
utf8mb4	UTF-8 Unicode	utf8mb4_0900_ai_ci	4
```
- 运行mysql设置字符集和Collation
```
mysqld —-character-set-server=utf8mb4 —-collation-server=utf8mb4_bin
```
- 查询mysql数据库服务器下schema的字符集
```
SELECT SCHEMA_NAME 'database', DEFAULT_CHARACTER_SET_NAME 'charset', 
DEFAULT_COLLATION_NAME 'collation' FROM information_schema.SCHEMATA;
```
- 修改数据库的字符集及Collation
```
ALTER DATABASE mysql_study CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```
- 修改数据库对象表和字段的字符集及Collation在此不例举

### 总结


### 说明
- 字符集和字符集编码是两个概念,ASCII字符集的编码是ASCII编码,Unicode字符集的编码存在多种,UTF-8、UTF-16等


### 参考资料
[字符、字符集、字符编码解惑](https://dailc.github.io/2017/05/03/char_charset_charEncoding.html)  
[Unicode 及编码方式概述](https://www.ibm.com/developerworks/cn/java/unicode-programming-language/index.html)  
[深入分析 Java 中的中文编码问题](https://www.ibm.com/developerworks/cn/java/j-lo-chinesecoding/index.html)
[深入分析 Java 中的中文编码问题](https://www.ibm.com/developerworks/cn/java/j-lo-chinesecoding/)


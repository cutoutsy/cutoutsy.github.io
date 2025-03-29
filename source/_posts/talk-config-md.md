---
layout: post
title: "谈谈那些配置文件"
date: 2015-09-20 21:35:36
tags: 
		- 配置文件
		- JAVA
---
### 0x00 写在前面
配置文件是什么东东，配置文件相信用过电脑的同学都接触过，有时候打游戏过程中出现问题，在网上寻找解决方法时，就会涉及到修改配置文件。在系统级，有一些用户的配置文件，用户在使用软件时，软件系统为用户所要加载所需环境的设置和文件的集合。它包括所有用户专用的配置设置，如程序项目、屏幕颜色、网络连接、打印机连接、鼠标设置及窗口的大小和位置等。经常使用Linux的话，那就避免不了与配置文件打交道。
<!-- more -->
### 0x01 常见的配置文件
#### **ini配置文件**
这种配置文件经常使用windows的童鞋可能见得比较多，在Windows系统中，INI文件是很多，最重要的就是“System.ini”、“System32.ini”和“Win.ini”。该文件主要存放用户所做的选择以及系统的各种参数。.ini文件是Initialization File的缩写，即初始化文件，是windows的系统配置文件所采用的存储格式。不过在其他软件中也有使用，用来配置应用软件以实现不同用户的要求,比如php的配置文件php.ini。
格式：
节：［section］
参数（键＝值）：name=cutoutsy
注释：注解使用分号表示，在分号后面的文字，直到该行结尾都全部为注解。
实例：
```ini
; exp ini file
[port]
portname=COM4
port=4
```
#### **json配置文件**
JSON是一种轻量的数据交换格式，易于人阅读和编写，同时也易于机器解析和生成(网络传输速率)。
JSON语法规则：

- 数据在键值对中
- 数据由逗号分隔
- 花括号保存对象
- 方括号保存数组

JSON值：

 - 数字（整数或浮点数）
 - 字符串（在双引号中
 - 逻辑值（true 或 false）
 - 数组（在方括号中）
 - 对象（在花括号中）
 - null

基础结构：对象和数组，通过这两种结构就可以组成复杂的数据结构。
1、对象：对象在js中表示为“{}”括起来的内容，数据结构为 {key：value,key：value,...}的键值对的结构，在面向对象的语言中，key为对象的属性，value为对应的属性值，所以很容易理解，取值方法为 对象.key 获取属性值，这个属性值的类型可以是 数字、字符串、数组、对象几种。
2、数组：数组在js中是中括号“[]”括起来的内容，数据结构为 ["java","javascript","vb",...]，取值方式和所有语言中一样，使用索引获取，字段值的类型可以是 数字、字符串、数组、对象几种。

比如我们用来翻墙的shadowsocks的config.json
```JSON
{
	"server":"remote-shadowsocks-server-ip-addr",
	"server_port":443,
	"local_address":"127.0.0.1",
	"local_port":1080,
	"password":"your-passwd",
	"timeout":300,
	"method":"aes-256-cfb",
	"fast_open":false,
	"workers":1
}
```
很多语言都支持JSON的解析，使用比较广泛。
#### **xml配置文件**
XML配置文件相信写JAVA WEB的童鞋肯定不会陌生，使用ssh框架的时候需要配置大量的XML配置文件。
XML：Extensible Markup Language，可扩展标记语言。
语法：

- 任何的起始标签都必须有一个结束标签。
- 可以采用另一种简化语法，可以在一个标签中同时表示起始和结束标签。这种语法是在大于符号之前紧跟一个斜线（/），例如<name/>。XML解析器会将其翻译成<name></name>。
- 标签必须按合适的顺序进行嵌套，所以结束标签必须按镜像顺序匹配起始标签。
- 所有的特性都必须有值。
- 所有的特性都必须在值的周围加上双引号。

```XML
<?xml version="1.0"?>
<note>
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note>
```
这个用得也是灰常广泛，很多语言都有很好的解析。
#### **properties配置文件**
.properties是JAVA的一种配置文件，主要用于表达配置信息。
语法：
参数（键＝值）：name=cutoutsy
注释：注解使用＃表示，在＃后面的文字，直到该行结尾都全部为注解。
实例：

```properties
#格式良好的properties文件 
aaa=111 
bbb=222
```
这个配置文件主要是在JAVA中使用，一个好的程序应该是基于配置的，嘻嘻。

#### **yaml配置文件**
首次见这个后缀名的文件是在学习Storm的时候，里面有个配置文件storm.yaml,我最开始不清楚这种语言格式，没有加空格，坑了我两天......
YAML="YAML Ain ’ t a Markup Language”，即：YAML 不是一种标记语言的递归缩写。
格式：
多行缩进
数据结构可以用类似大纲的缩排方式呈现，结构通过缩进来表示，连续的项目通过减号“-”来表示，map结构里面的key/value对用冒号“:”来分隔。样例如下
```YAML
house:
  family:
    name: Doe
    parents:
      - John
      - Jane
    children:
      - Paul
      - Mark
      - Simone
  address:
    number: 34
    street: Main Street
    city: Nowheretown
    zipcode: 12345
```
注意：
1. 字串不一定要用双引号标识；
2. 在缩排中空白字符的数目并不是非常重要，只要相同阶层的元素左侧对齐就可以了（不过不能使用TAB字符）；
3. 允许在文件中加入选择性的空行，以增加可读性；
4. 在一个档案中，可同时包含多个文件，并用“——”分隔；
5. 选择性的符号“...”可以用来表示档案结尾（在利用串流的通讯中，这非常有用，可以在不关闭串流的情况下，发送结束讯号）

单行缩写
YAML也有用来描述好几行相同结构的数据的缩写语法，数组用'[]'包括起来，hash用'{}'来包括。因此，上面的这个YAML能够缩写成这样:
```YAML
house:
  family: { name: Doe, parents: [John, Jane], children: [Paul, Mark, Simone] }
  address: { number: 34, street: Main Street, city: Nowheretown, zipcode: 12345 }
```
最后贴一个storm的配置文件：

#### **conf配置文件**
conf是config的缩写，就是配置文件的意思，这种配置文件也很常见。
常见的比如Apache的配置文件http.conf
语法：
参数（键 值）：name cutoutsy
注释：注解使用＃表示，在＃后面的文字，直到该行结尾都全部为注解。
实例：
```conf
# Mutex: Allows you to set the mutex mechanism and mutex file directory
# for individual mutexes, or change the global defaults
#
# Uncomment and change the directory if mutexes are file-based and the default
# mutex file directory is not on a local disk or is not appropriate for some
# other reason.
#
# Mutex default:/private/var/run

#
# Listen: Allows you to bind Apache to specific IP addresses and/or
# ports, instead of the default. See also the <VirtualHost>
# directive.
#
# Change this to Listen on specific IP addresses as shown below to
# prevent Apache from glomming onto all bound IP addresses.
#
#Listen 12.34.56.78:80
Listen 80

#
# Dynamic Shared Object (DSO) Support
# Example:
# LoadModule foo_module modules/mod_foo.so
LoadModule authn_core_module libexec/apache2/mod_authn_core.so
LoadModule authz_host_module libexec/apache2/mod_authz_host.so
LoadModule authz_groupfile_module libexec/apache2/mod_authz_groupfile.so
LoadModule authz_user_module libexec/apache2/mod_authz_user.so
```
### 0x03 最后说两句
当然还有很多其他的配置文件，我只是列举一些常见的配置文件。当我们遇到遇到需要配置这些文件的时候，我们要注意格式，不然配置不会其作用或者软件不会运行。当我们自己在软件里使用配置文件时，也要注意配置文件的选择，选择一种适合自己软件的配置文件。



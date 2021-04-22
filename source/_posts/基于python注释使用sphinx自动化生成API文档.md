---
layout: post
title: 基于python注释使用sphinx自动化生成API文档
categories: Python
date: 2020-04-25 15:37:58
tags:
    - Python
    - sphinx
    - 文档
---
### 写在前面
我们平时在使用python包过程中，经常都能看到包对应的文档，拉到网页底部，往往能看到"Powered by Sphinx"，sphinx是一种基于Python的文档工具，当然对其他文档也有支持。本文就主要基于代码已有的注释，使用sphinx自动化生成API文档。

### sphinx安装
```shell
pip install sphinx
```

版本：v3.0.2

这个版本和之前在网上教程看的好多例子不太一样。

### 一个简单的例子
**新建一个项目**

<!--more-->

项目目录如下：

![项目目录1](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_1.png)

`demo1.py`代码如下：
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
"""
desc

Date: 2020/4/22

"""


class Demo1():
    """类的功能说明"""

    def add(self, a, b):
        """两个数字相加，并返回结果"""
        return a+b

    def google_style(self, arg1, arg2):
        """函数功能.

        函数功能说明.

        Args:
            arg1 (int): arg1的参数说明
            arg2 (str): arg2的参数说明

        Returns:
            bool: 返回值说明

        """
        return True
```


这是一个简单的项目目录，作为第三方包来说，有时还包含子模块，但是已验证都是支持的。

在源代码同级目录下新建一个文档doc，现在目录结构如下：

![项目目录2](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_2.png)


### sphinx-quickstart
在doc目录下，使用命令`sphinx-quickstart`，除了自己项目的配置，其他配置可以默认。

命令执行情况如下：

![sphinx-quickstart](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_3.png)

这个输出和网上现在很多教程不一样，没有一些扩展的设置，类似下面：

![extensions](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_4.png)

但是我们可以在`source/conf.py`中进行配置

### conf.py配置
配置extensions，修改source/conf.py文件内容：
```python
extensions = [
    'sphinx.ext.autodoc',
    'sphinx.ext.doctest',
    'sphinx.ext.intersphinx',
    'sphinx.ext.todo',
    'sphinx.ext.coverage',
    'sphinx.ext.mathjax'
]
```
如果需要自动生成API文档，需要`sphinx.ext.autodoc`

配置项目路径：

取消13~15行的注释，修改为下面的内容：
```python
import os
import sys
sys.path.insert(0, os.path.abspath('../../'))
```

这块路径使用`../../`是因为我们的demo是一个包，如果demo下面没有`__init__.py`文件，则可以路径为`../../demo`。

### 生成rst文件
使用sphinx-apidoc生成rst文件，-o 后面跟的是保存rst文件的路径，**你的index.rst文件在哪个目录，就指定哪个目录**，然后最后面是代码路径

在doc目录下，执行结果如下：
![sphinx-apidoc](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_5.png)

### 生成HTML
在doc目录下，使用make命令生成html文件

生成结果如下：


使用前，先清除一下之前的生成文件
```shell
make clean
```
生成html
```shell
make html
```
过程如图：

![make html](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_6.png)

这块有个红色的warning，我们后面再来解决这个问题，先暂且放着。

### 展示效果
现在我们用浏览器打开doc/build/html/index.html，显示如下：
![展示效果1](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_7.png)

这是不是和我们平时看到的python文档不太一样，那是因为我们的主题没有选对

### 改变sphinx主题
python sphinx的主题包很多，现在大多数用得多的是readthedoc风格的，主题名字叫sphinx_rtd_theme。

**安装主题**
```shell
pip install sphinx_rtd_theme
```
**导入模块**：修改source/conf.py文件
```python
# 导入模块
import sphinx_rtd_theme

# html_theme = "alabaster"修改如下，加上html_theme_path
html_theme = "sphinx_rtd_theme"
html_theme_path = [sphinx_rtd_theme.get_html_theme_path()]
```
**重新生成**：在doc目录下
```shell
make clean
make html
```
**显示效果**
![显示效果2](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_8.png)

现在看着是不是熟悉了很多

### 采坑
不知道大家有没有发现，上面生成的文档左边导航栏下面是没有内容`CONTENTS`的，本来应该是像下面这样的：
![显示效果3](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_9.png)
还记得上面提到的在`make html`时的那个Warning么，这就是那个导致的

那个warning的意思是说，modules.rst没有被包含，没有被什么包含呢，是没有在index.rst里面包含，毕竟我们显示是用的index.rst。

所以我们需要在index.rst里面加上modules，不知道是不是这个版本的问题，我在好多教程里面都没有提到这个，所以踩了个坑。

修改后如下(source/index.rst)：
```shell
.. demo documentation master file, created by
   sphinx-quickstart on Thu Apr 23 10:09:09 2020.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to demo's documentation!
================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   modules

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

```
之后，再运行`make clean`和`make html`，则warning消失，页面上会显示`CONTENTS`

![make html 2](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_sphinx_10.png)

### Nginx发布
安装nginx后，在配置文件(Linux一般在: /etc/nginx/nginx.conf)里面，在http下面增加如下配置：
```python
server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  localhost; 
        root         /data/www/html;  //将doc/build/html复制到/data/www/html

        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
}

```
重启nginx
```shell
nginx -s reload
```
打开对应网站即可看到文档。

### 参考资料

[使用sphinx快速为你python注释生成API文档](https://blog.csdn.net/sinat_29957455/article/details/83657029)

[自动生成Python项目文档](https://www.jianshu.com/p/d4a1347f467b)

[自动生成API文档](https://sphinx-apidoc.readthedocs.io/zh_CN/latest/gettingstarted/genapi.html)

[toctree can't find modules.rst in source directory](https://groups.google.com/forum/#!topic/sphinx-users/9-nngSn8018)

[搭建 Nginx 静态网站](https://juejin.im/post/5c286c56e51d451d460342b4)

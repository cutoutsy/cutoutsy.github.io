---
layout: post
title: pypi搭建私有服务器及使用
categories: Python
date: 2020-03-25 23:19:46
tags:
    - python
    - pypi
    - package
---
### 写在前面
pip安装的python包，默认从https://pypi.python.org/pypi 上下载的。但是有时候公司内部使用的组件，不方便开放到外网，这个时候就需要搭建自己的pypi服务器了。官网提供多种解决方案来搭建私有pypi服务器。

### 安装pypi
直接使用pypi安装即可
```shell
pip install pypiserver
```
然后建立存放packages的文件夹，我这块是建在用户根目录下
```shell
mkdir ~/packages
```

### 配置
如果是通过拷贝的方式，即将包拷贝到packages下，则可以不用配置用户名密码。
上传package需要用户名密码，密码文件使用命令htpasswd命令生成。
```shell
pip install passlib
apt-get update # 如果直接安装不成功，则更新下源
apt-get install apache2-utils
htpasswd -sc .htaccess username # username是上传package需要的用户名，回车后会提示输入密码，用户名密码在后面的上传包的时候会用到
```

### 启动
pypi-server应该在后台运行，简单的话可以使用nohup命令

-P选项指定密码文件

-p选项指定开放的端口
```
nohup pypi-server -p 8080 -P ~/.htaccess ~/packages &
```
通过浏览器访问http://服务器:8080，可以看到如下的欢迎页：

![pypi_server](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/pypi_server.png)


### 上传前配置
在用户的根目录下新建.pypirc，内容如下：
```python
[distutils]
index-servers =
    internal

[internal]
repository: http://pypi服务器ip:8080
username: username
password: password
```
其中**username**和**password**就是配置pypi服务器时设置在.htaccess文件中的用户名密码

### 打包
创建一个简单的包来演示打包发布到私用服务器上
```shell
cd /tmp
mkdir demo
cd demo
mkdir demo_package
touch demo_package/__init__.py
```
在demo中创建一个setup.py文件，写入如下代码:
```python
from setuptools import setup, find_packages
setup(
    name = "demo",
    version = "0.1",
    packages = find_packages(),
)
```
当前的目录结构：
```shell
demo
|-- demo_package
|   `-- __init__.py
`-- setup.py
```
执行打包命令：
```shell
python3 setup.py sdist bdist_wheel
```
命令执行完，目录下会生成dist目录，然后使用twine上传包
```shell
pip install twine
twine upload -r internal ./dist/demo-0.1.tar.gz
```
登录服务器，在package目录就能看到上传的包了。

### 使用
我们使用pip进行安装，可以用-i参数指定从私有服务器下载，用--trusted-host信任私有服务器
```
pip install --trusted-host pypi私有服务器 -i http://pypi服务器:8080/simple/ uplib
```
后面就可以开心的使用了~

### 参考资料

https://www.jianshu.com/p/e104fcbbaa31

https://www.jianshu.com/p/a7a5ce79d2fb

https://www.jianshu.com/p/c260b59cd3d0

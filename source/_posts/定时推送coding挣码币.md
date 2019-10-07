---
layout: post
title: 定时推送coding挣码币
date: 2016-03-29 16:13:35
tags: 
		- coding 
		- 码币
---
今天早上浏览推酷的时候，看到一篇[一个结合crontab定时推送github或coding库小玩意](http://www.tuicool.com/articles/iAzQjuz)的文章，觉得很有意思，因为我平时主要就是在用coding来托管代码，每天推送代码可挣码币，码币可以换东西。可是不可能每天都会推送代码，有个脚本来执行就靠谱多了，这篇文章说的就是这个，在阿里云上跑了一个，基本符合要求。

<!-- more -->

我只是在coding上定时推送代码，没有在github上。下面说一下我在实现中遇到的一些问题：
首先coding上的git版本要求是使用 Git 1.8.0 以上的版本，而我在阿里云上的版本却是1.7，尝试了下，不能通过https下载，不知道能不能通过ssh下载。最好安装一个比较新的版本，现在都git最新版本都2.8.0了。安装git之后之后需要配置SSH公钥配置，这样在提交代码的时候就不需要输入用户名密码了，利于用脚本操作。具体如何配置可以看coding上的帮助文档。

在coding上创建一个项目，初始化时不选择README.md和LICENSE，我创建的项目名叫mabi，哈哈。然后clone项目，并修改.git/config文件中的[remote "origin]"节点下url路径，修改为你自己创建项目的地址，注意是ssh地址，不是https地址。
```shell
git@git.coding.net:xxx/mabi.git
```

修改并添加文件bug.md，然后push到coding上去。

提交代码的脚本是用python写的，所以运行环境必须要有python，脚本代码如下：
```python
#!/usr/bin/env python
# -*- coding: utf8 -*-
# main @ Python
import sys,os,time,subprocess,random

file_name = 'bug.md'
#修改为你的本地项目文件地址
file_path = '/root/repository/mabi/'

def check_file():
	if not os.path.exists(file_path + file_name):
		f = open(file_path + file_name,'w')
		f.write(str(time.time()) + '\n')
		f.close()

def add_file_line():
	f = open(file_path + file_name,'a+')
	f.write(str(time.time()) + '\n')
	f.close()

def git_add():
	cmd = ['git', 'add', '.']
	p = subprocess.Popen(cmd,cwd=file_path)
	print p.wait()

def git_commit():
	centext = "'upload bug info - " + str(time.time()) + "'"
	cmd = ['git', 'commit', '-m',centext]
	p = subprocess.Popen(cmd,cwd=file_path)
	print p.wait()

def git_push():
	cmd = ['git', 'push', '-u','origin','master']
	p = subprocess.Popen(cmd,cwd=file_path)
	print p.wait()

def cmd_list():
	cmd_list = []
	cmd_list.append("git add .")
	cmd_list.append("git commit -m 'upload bug info - " + str(time.time()) + "'")
	cmd_list.append("git push -u origin master")
	return cmd_list
	
def cmd_go():
	cmd_str_list = cmd_list()
	for cmd_go in cmd_str_list:
		subprocess.call(cmd_go,shell=True)
		
def file_handle():
	check_file()
	add_file_line()
	#time.sleep(random.randint(0, 360))	#不需要可注释
	#cmd_go()
	git_add()
	git_commit()
	git_push()

if __name__ == "__main__":
	file_handle()	
	print 'done.'
```
修改的地方只有一处，就是file_path = '/root/repository/mabi/'修改为你自己的项目地址

定时：
设置 crontab 定时任务
```shell
00 1 * * * python main.py的绝对路径
```
这样每天凌晨一点的时候就会提交代码
把以上操作配置在一台不咋关机的服务器上，比如阿里云，这样你就能每天挣码币了。

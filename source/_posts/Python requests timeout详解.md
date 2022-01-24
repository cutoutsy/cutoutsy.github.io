---
layout: post
title: Python requests timeout详解
categories: python
date: 2022-01-18 20:02:11
tags:
    - python
    - requests
    - timeout
---
### 0x01 源于线上Bug

某天下午正在愉快的写着代码，突然某服务客户端报错，看了下报错详细信息，是报的超时错误，客户端设置的连接超时时间是5s，一般不可能报错的，开始查日志，定位问题...

从服务记录的日志来看，是服务里面会请求模型服务，这块耗时超过了5s才返回。整个服务是使用的gunicorn + flask的方式，部署在公司私有云上，请求模型服务使用的是python requests包。

这块代码如下（经过一定的简化）：
<!--more-->
```python
start = time.time()
# 设置超时时间为 1 s
try:
    result = requests.post(model_url + '/predict', json=requests_input, timeout=1)
    log_wrap.debug("model predict cost time: {:.2f}s".format(time.time() - start))
    if result.status_code != 200:
        error_msg = "model request error, status_code: {}, msg: {}".format(result.status_code, result.json())
        log_wrap.error(error_msg)
    else:
        output = result.json()["output"]
except Exception as e:
    # 异常处理逻辑
    ...
```

从代码上看，使用requests时设置了timeout为1s，还加上了异常捕获，但是日志打印出来显示model predict耗时花了5s。

这个报错是偶发的，平均可能两天左右报一次超时错误。

### 0x02 Debug过程

python requests包想必大家都很熟悉了，感觉应该不会有问题。因为日志记录的模块用的不是python标准的，自己封装了一下，于是怀疑是不是日志打印的时候有问题，于是上线了一版python标准日志打印。代码修改如下：
```python
result = requests.post(model_url + '/predict', json=requests_input, timeout=1)
logger.debug("model predict cost time: {:.2f}s".format(time.time() - start))  # python标准日志打印
log_wrap.debug("model predict cost time: {:.2f}s".format(time.time() - start))
```
上线后，超时报错又出现了，查看日志，发现两个日志打印的记录是一样的，这时可以排除是日志的问题。

接下来就走了弯路了（其实还是应该着重解决为啥设置了超时时间，没有生效），开始想是不是模型服务或者模型服务前面Kong网关的问题，查了下模型服务网关上的日志，发现网关整体处理时间以及上游模型服务处理时间都很短，基本都是一两百毫秒。

由于Kong网关的无法定位具体某个请求，但是网关日志会记录referer，于是在request post时候，生成了一个唯一的traceid放在header里面，并记录了发出请求的时间点，上线了一个版本，请求的代码变成了如下：
```python
traceid = str(uuid.uuid1())
logger.debug({"message": "micro model request."})
result = requests.post(model_url + '/predict', json=requests_input, headers={"referer": traceid}, timeout=1)
logger.debug("model predict cost time: {:.2f}s".format(time.time() - start))  # python标准日志打印
log_wrap.debug("model predict cost time: {:.2f}s".format(time.time() - start))
```
上线后，出现超时报错后，看到模型服务网关上记录的时间点比服务里面请求发出的时间点大5s，这个时候，以为是模型服务或者模型服务前网关的问题，就开始找相关同学排查。

相关同学排查后，说没有问题，但是我感觉还是有问题的。

这个时候，我发现其实不应该去关注模型服务或者网关的问题，应该解决超时参数为什么不生效的问题。不管模型服务或者网关是什么情况，只要超时生效，那么服务就不会超时。

### 0x03 requests timeout含义

这个时候，我又去仔细看了下requests中timeout的含义，官网上是这样说的
> timeout is not a time limit on the entire response download; rather, an exception is raised if the server has not issued a response for timeout seconds (more precisely, if no bytes have been received on the underlying socket for timeout seconds). If no timeout is specified explicitly, requests do not time out.

大致意思是timeout不是针对整个请求的，这个主要是两个时间：
> * 连接超时: 指的是客户端实现到远端机器端口的连接时，等待的秒数
> * 读取超时：指的是客户端等待服务器发送请求的时间。（特定地，它指的是客户端要等待服务器发送字节之间的时间。在 99.9% 的情况下这指的是服务器发送第一个字节之前的时间）

因为发送第一个字节，那么存在返回数据很大，下载数据耗时较长，但是不会触发超时。
下面我们来测试一下，代码如下：
```python
import time
import requests

t1 = time.time()
url5 = 'http://ipv4.download.thinkbroadband.com/5MB.zip'
re = requests.get(url5, timeout=0.5)

print("reqtimes: ", t2 - t1)

print(re.status_code)
```
运行结果：

![运行结果](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/python_requests_01.png)

可以看到：运行时间耗费了2.9s，但是并没有报超时的异常。

这个时候需要寻找另外的解决方案。

### 0x04 解决方案

这个时候，从stackoverflow看到可以使用python signal来限制整个请求的耗时。

给的例子：
```python
import signal
from time import sleep

class TimeoutException(Exception):
    """ Simple Exception to be called on timeouts. """
    pass

def _timeout(signum, frame):
    """ Raise an TimeoutException.

    This is intended for use as a signal handler.
    The signum and frame arguments passed to this are ignored.

    """
    # Raise TimeoutException with system default timeout message
    raise TimeoutException()

# Set the handler for the SIGALRM signal:
signal.signal(signal.SIGALRM, _timeout)
# Send the SIGALRM signal in 10 seconds:
signal.alarm(10)

try:    
    # Do our code:
    print('This will take 11 seconds...')
    sleep(11)
    print('done!')
except TimeoutException:
    print('It timed out!')
finally:
    # Abort the sending of the SIGALRM signal:
    signal.alarm(0)
```

自已快速学习了下signal模块，信号是Unix系统中常见的一种进程间通信方式（IPC），例如我们在杀进程的时候命令`kill -9 pid`，这里的`-9`对应的就是SIGKILL信号，9是这个信号的编号，SIGKILL是它的名称。

信号的使用场景，与其他进程间通信方式（例如管道、共享内存等）相比，信号所能传递的信息比较粗糙，只是一个整数。但正是由于传递的信息量少，信号也便于管理和使用，可以用于系统管理相关的任务。例如通知进程终结、中止或者恢复等。

**定时发出SIGALRM信号**
```python
signal.signal(signal.SIGALRM, _timeout)
```
定义信号处理函数，这里的处理是抛出TimeoutException异常
```
signal.alarm(10)
```
设置多长时间后向进程自身发送SIGALRM信号，可以避免无限期的阻塞I/O操作或者其他其他系统调用。
```python
signal.alarm(0)
```
这是取消发送SIGALRM信号，在finally中执行。

修改后，线上代码变成：
```python
import time
import signal
import requests

class TimeoutException(Exception):
    """ Simple Exception to be called on timeouts. """
    pass

def _timeout(signum, frame):
    """ Raise an TimeoutException.

    This is intended for use as a signal handler.
    The signum and frame arguments passed to this are ignored.

    """
    raise TimeoutException()

signal.signal(signal.SIGALRM, _timeout)
signal.alarm(1)
try:
    result = requests.post(model_url + '/predict', json=requests_input, timeout=1)
    logger.debug("model predict cost time: {:.2f}s".format(time.time() - start))  # python标准日志打印
    log_wrap.debug("model predict cost time: {:.2f}s".format(time.time() - start))
except Exception as e:
    # 定义自己的异常处理逻辑
    ...
finally:
    signal.alarm(0)

```

上线后，问题解决。

### 0x05 总结
工作以来也经历了几次线上Bug的解决，对于Bug，主要首先主要是两个点，首先是是否可复现，如果可复现，那么一般来说定位或者解决问题都是比较快的，但是往往有些bug复现不是那么容易的；其次就是问题定位，这里说的定位，是针对有的服务链路比较长，第三方服务也比较多，这个时候能准确定位是哪个环节出了问题就比较重要了，有的时候并不能一次就定位到，往往要想办法加日志来定位，定位到后，也就比较好解决了。

这可能就是程序员头发少的原因吧~

### 0x06 参考资料

[https://www.cnblogs.com/gl1573/p/10129382.html](https://www.cnblogs.com/gl1573/p/10129382.html)

[https://stackoverflow.com/questions/13573146/how-to-perform-time-limited-response-download-with-python-requests](https://stackoverflow.com/questions/13573146/how-to-perform-time-limited-response-download-with-python-requests)

[https://stackoverflow.com/questions/21965484/timeout-for-python-requests-get-entire-response](https://stackoverflow.com/questions/21965484/timeout-for-python-requests-get-entire-response)

[https://docs.python.org/3.3/library/signal.html](https://docs.python.org/3.3)

[https://juejin.cn/post/6844903733466251272](https://juejin.cn/post/6844903733466251272)
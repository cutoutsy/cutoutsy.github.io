---
layout: post
title: Python logging.config.dictConfig中Filter的使用
categories: Python
date: 2020-04-01 22:197:46
tags:
    - python
    - log
    - dictConfig
---
### 写在前面
日志记录一直以来是工程中比较重要的问题，良好的日志记录可以快速排查Bug，也能对服务运行情况有一个良好的把握

### 配置日志的几种方式

> * 使用Python代码显式的创建loggers, handlers和formatters并分别调用它们的配置函数 
> * 创建一个日志配置文件，然后使用**fileConfig**()函数来读取该文件的内容
> * 创建一个包含配置信息的dict，然后把它传递给**dictConfig**()函数

<!--more-->

这次先主要讲一下dictConfig()的使用

### 使用dictConfig()函数实现日志配置
Python 3.2中引入了一种新的配置日志的方式，用字典来保存logging配置信息。相比较于基于配置文件来保存logging配置信息的方式来说，功能更加灵活强大。我们可以在代码里构建这个字典，或者通过自己喜欢的配置文件来生成字典（不局限于日志配置文件格式），甚至可以通过socket接收pickle序列化后的配置信息。

下面来看一个在代码构建dictConfig()的例子：
```python
import logging, logging.config
from structlog import getLogger, configure, processors, stdlib

class LoggingErrorFilter(logging.Filter):
    def filter(self, record):
        if not hasattr(record, 'schema'):
            record.schema = "com.xxx.xxx" 
        return True

def request_logger(logfile_name):
    logging.config.dictConfig({
        'version': 1,
        'disable_existing_loggers': False,
        'formatters': {
            'f_json': {
                'format': '%(ts)s %(run_time)f %(proj)s',
                'class': 'pythonjsonlogger.jsonlogger.JsonFormatter'
            }
        },
        'filters': {
            'errorfilter': {
                '()': LoggingErrorFilter,
            }
        },
        'handlers': {
            'h_json': {
                'class': 'logging.handlers.RotatingFileHandler',
                'formatter': 'f_json',
                'filename': logfile_name,
                'level': 'INFO',
                'filters': ['errorfilter'],
            }
        },
        'loggers': {
            'FileLogger': {
                'handlers': ['h_json'],
                'level': 'INFO'
            }
        }
    })

    configure(
        logger_factory=stdlib.LoggerFactory(),
        processors=[
            stdlib.render_to_log_kwargs]
    )

    struct_logger = getLogger("FileLogger")
    return struct_logger
```
代码里使用了structlog，但并不影响理解dictConfig。

这个日志是为了记录线上接口的调用数量，使用方式如下：
```python
logger = request_logger("log_file_name")
start = time.time()
project = "demo"
ts = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
time.sleep(0.5)
run_time = format((time.time() - start), '.3f')
logger.info({"ts": ts, "run_time": float(run_time), "proj": project})
```
从使用上来说，这样其实不友好，ts需要我们在使用的时候给出，project也需要再代码中给出，ts能不能默认记录呢，project能不能通过传入配置项的方式呢。

我们可以通过filter来实现这个功能，自定义filter，在初始化时传入参数。比如采用如下方式定义Filter.
```python
class LoggingErrorFilter(logging.Filter):
    
    def __init__(self, proj_name, ts):
        self.proj = proj_name
        self.ts = ts
        super(LoggingErrorFilter, self).__init__()
    
    def filter(self, record):
        record.proj = self.proj
        record.ts = self.ts
        if not hasattr(record, 'schema'):
            record.schema = "com.xxx.xxx" 
        return True
```

在使用代码显式创建logger里面，filter是将类实例化后加入logger中，而dictConfig却不能，下面的代码会报错：
```python
'filters': {
            'errorfilter': {
                '()': LoggingErrorFilter(proj_name, ts),
            }
        },
```
查了很多中文资料，都没有人说到这个问题。最后查了下英文资料，说到了这个问题。

参数是在下面配置的，比如像下面代码这样就可以：
```python
'filters': {
            'errorfilter': {
                '()': LoggingErrorFilter,
                'proj_name': proj_name,
                'ts': datetime.now().strftime('%Y-%m-%d %H:%M:%S'),
            }
        },
```
这样就可以使用，弄了一晚上，踩了一些坑。



### 参考资料

[structlog](http://www.structlog.org/en/stable/getting-started.html)

[install filter on logging level in python using dictConfig](https://stackoverflow.com/questions/21455515/install-filter-on-logging-level-in-python-using-dictconfig)

[python之配置日志的几种方式](https://www.cnblogs.com/yyds/p/6885182.html)

[Python日志库logging总结-可能是目前为止将logging库总结的最好的一篇文章](https://juejin.im/post/5bc2bd3a5188255c94465d31)

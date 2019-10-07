---
layout: post
title: 关于Httpclient中超时的设置－记一次程序调试过程
date: 2016-04-04 17:48:13
tags:
		- Java
		- Httpclient
---
最近针对校内论坛写了一个机器人，就是可以自动回复帖子，可以挣取积分、金币啥的；用Java的Httpclient4.4写的，原来我就写过针对学校论坛的自动回复，不过那个要在学校内网才能使用，我又没有学校内网的服务器，这几天发现我们学校论坛为了方便在校外访问，有一个移动版的论坛。顿时就想改一下软件然后放到我的阿里云主机上去，这样就可以7x24小时帮我回复挣积分了，23333......

<!-- more -->

可以当我改完放到服务器上去跑的时候，发现每当回复一个帖子之后，整个程序就卡住了，后面的语句都不执行了，也没有报错信息的输出。经过调试，我发现是在org.apache.http.impl.execchain下的MainClientExec类里execute方法下的如下代码段卡住的
```JAVA
final int timeout = config.getConnectionRequestTimeout();
managedConn = connRequest.get(timeout > 0 ? timeout : 0, TimeUnit.MILLISECONDS);
```
由此可见是由于某个超时时间没有设置引起的。google了下Httpclient下的超时时间的设置，主要有三个超时时间的设置，ConnectionRequestTimeout、ConnectionTimeout、SocketTimeout，平时我们主要会设置的是后两个。
接着我去RequestConfig类里查看了下关于ConnectionRequestTimeout的解释

```JAVA
/**
     * Returns the timeout in milliseconds used when requesting a connection
     * from the connection manager. A timeout value of zero is interpreted
     * as an infinite timeout.
     * <p>
     * A timeout value of zero is interpreted as an infinite timeout.
     * A negative value is interpreted as undefined (system default).
     * </p>
     * <p>
     * Default: {@code -1}
     * </p>
     */
    public int getConnectionRequestTimeout() {
        return connectionRequestTimeout;
    }

```

从注释可以看出这个超时是在从连接管理器中取出连接的超时时间，而0是被解释为无限的时间的。而上面出现问题的代码，当我们没有设置这个参数的时候时，是默认给0的，所以才会造成无限等待的情况，看着就像程序卡住了一样，还没有报错信息输出。

接着我在代码里设置了这个参数，并将其超时时间设置为1s；这样就出现了报错信息：
```JAVA
org.apache.http.conn.ConnectionPoolTimeoutException: Timeout waiting for connection
    at org.apache.http.impl.conn.tsccm.ConnPoolByRoute.getEntryBlocking(ConnPoolByRoute.java:417)
    at org.apache.http.impl.conn.tsccm.ConnPoolByRoute$1.getPoolEntry(ConnPoolByRoute.java:300)
    at org.apache.http.impl.conn.tsccm.ThreadSafeClientConnManager$1.getConnection(ThreadSafeClientConnManager.java:224)
    at org.apache.http.impl.client.DefaultRequestDirector.execute(DefaultRequestDirector.java:401)
    at org.apache.http.impl.client.AbstractHttpClient.execute(AbstractHttpClient.java:820)
    at org.apache.http.impl.client.AbstractHttpClient.execute(AbstractHttpClient.java:754)
    at org.apache.http.impl.client.AbstractHttpClient.execute(AbstractHttpClient.java:732)
```

根据这个报错信息，我google了一下，在stackoverflow上找到了答案，大概的意思就是说，请求返回的response需要被消费掉，于是我去看我以前写的代码，由于我在登陆的时候，需要抽取返回头部的Cookies，所以我先获得response，后面需要内容时，我在把response解析出来，然而解析之后我却没有关闭response，所以造成这样的错误，于是我加上如下代码：

```JAVA
finally{
	if(response != null){
		try{
			response.close();
		}catch(IOException e){
			e.printStackTrace();
		}
	}
}
```
成功解决了bug。

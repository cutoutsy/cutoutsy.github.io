---
layout: post
title: "Java里执行js代码"
date: 2015-06-11 00:11:44
tags: 
		- JAVA 
		- js
---
在用java写爬虫的过程中，有时我们需要执行js代码来模拟js渲染得到结果。当然也可以用phantomjs来写爬虫，但这不在本文的讨论范围内。
在DK1.6版添加了新的ScriptEngine类，允许用户直接执行js代码。
<!-- more -->
```java
public static void runJs() {
	try {
			ScriptEngineManager manager = new ScriptEngineManager();   
			ScriptEngine engine = manager.getEngineByName("javascript");     

			String jsFileName = "test.js";   // 读取js文件   
			FileReader reader = new FileReader(jsFileName);   // 执行指定脚本 
			engine.eval(reader);   

			if(engine instanceof Invocable) { 
			Double c = (Double)invoke.invokeFunction("merge", 2, 3);//调用merge方法，并传入两个参数 
            System.out.println("c:"+c);
			}   

			reader.close();
		} catch (Exception e) {
			e.printStackTrace();
		}  
		return re;
		  }
```

js文件代码是这样的：
```javascript
function merge(a, b) { 
 c = a * b; 
 return c; 
}
```

结果输出：5
这样就实现了在java里执行js代码

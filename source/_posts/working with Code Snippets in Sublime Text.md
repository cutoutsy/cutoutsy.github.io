---
layout: post
title: Working with Code Snippets in Sublime Text
tags:
	- code
	- sublime text
---

在我们的代码编写中，我们或多或少会存在一些常用的代码片段，开发者（包括我）都倾向于把一些常用的代码片段重复使用儿不是重复的编写。一种有效的做法就是把这些代码编写成snippts，这样就可以达到重复使用。

很多编辑器都支持这个功能，今天我就sublime text（代码编辑神器）来谈一下怎么在sublime text中使用snippets。
<!-- more -->
现在我就写这个hexo的markdown模板来使用sublime text的snippet。在我写博客的时候，每篇博客在开头都会写上这些代码

```markdown
---
layout: post
title: title
tags:
	- tag1
	- tag2
---
```
根据这个需求，我们可以来创建自己的snippets，打开menu -> Tools -> new Snippet...
```markdown
<snippet>
	<content><![CDATA[
Hello, ${1:this} is a ${2:snippet}.
]]></content>
	<!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
	<!-- <tabTrigger>hello</tabTrigger> -->
	<!-- Optional: Set a scope to limit where the snippet will trigger -->
	<!-- <scope>source.python</scope> -->
</snippet>
```
修改后变成如下的内容：
```markdown
<snippet>
	<content><![CDATA[
---
layout: post
title: ${1:title}
tags:
	- tag1
	- tag2
---

$2
]]></content>
	<!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
	<tabTrigger>markdown</tabTrigger>
	<!-- Optional: Set a scope to limit where the snippet will trigger -->
	<scope>text.html.markdown</scope>
</snippet>
```
编写之后保存命名为blog-header.sublime-snippet,保存到Packages/User之下，文件名前面的可以根据自己喜爱的方式定义，但是后缀必须是.sublime-snippet。保存结束后打开一个markdown文件，在控制面板中输入snippet就可以看到这个snippet的提示消息了。注意：要在markdown类型的文件下，在控制面板中输入snippet，在其他文件下面是不可以的。

这样我们就可以把平时代码编写过程中的重复代码编写snippet，省去我们的重复劳动，程序员都是偷懒的。

### 谈一下snippet里面的scope
scope:作用范围
上面填写的scope是怎么得到的呢，在subliem text里面可以通过快捷键ctrl+shift+p来读取当前文件的scope，显示在sublime tex界面的左下角。爆出来的内容可能是空格隔开的多个字符串，一般去第一个字符串就可以了。基本上各种代码的scope都是source开头，如soure.java,source,js,source.json等。其他的都是以text开头，如text.heml.markdown,text.plain。
在snippet里面定义多个scope
```markdown
<scope>text.html.markdown, text.plain</scope>
```
好的，今天关于在sublime text使用snippet就说这么多了，有啥不对的请指正。
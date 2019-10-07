---
layout: post
title: Mac下LaTeX环境的搭建
categories: 杂记
tags:
  - LaTeX
  - mac
date: 2018-10-24 16:50:29
---

### 写在前面
最近在做毕设相关的，准备用LaTeX来进行论文的编写，所以尝试在Mac下配置LaTeX的编写环境，在网上查了比较多的资料，都讲得不是很清楚，所以把我配置的过程记录下来，希望能有所帮助。

LaTeX是一种编程语言，用于电子排版，能够对文章进行十分精美的排版。根据给定的命令进行文档书写，就无需关心格式，而可以专注于内容的创作。
<!--more-->

### 软件安装
**MacTex安装**
LaTeX是一种语言，比如Java，那么CText就是集成运行环境，比如Eclipse或者IDEA。CTex是Windows平台下的，对于的Mac平台下为MacTex。下载地址[http://www.tug.org/mactex/](http://www.tug.org/mactex/)。
MacTex有两种版本，一种是全套的，基本都包含了，虽然有很多你可能都用不上，但是安装这个版本比较省心，可以避免出现一些问题。但是这个有个缺点，就是体积比较大。
另外一个版本是基本版，BasicTeX，这个包含了基本的运行软件，由于我电脑存储比较小，我安装的是这个版本。
安装位置：/usr/local/texlive/2018basic/
如果是完整版，安装位置为：/usr/local/texlive/2018/

**Visual Studio Code**
这是一个编辑器，写LaTeX就和我们写Java类似，首先需要安装Java运行环境，就是上面的MacTex，然后再用编辑器来编写代码，最后编译即可。
Visual Studio Code是微软出的一个文本编辑器，界面简单美观，插件丰富，打开大文件也很快。
推荐去官网下载安装，地址[https://code.visualstudio.com/](https://code.visualstudio.com/)。

**LaTeX Workshop插件安装**
上面提到vscode插件众多，LaTeX Workshop就是其中的一款，支持在vscode中编写LaTeX。
打开vscode，点击右边的扩展，或者使用快捷键"Command+Shift+x"，然后在搜索框中输入LaTeX Workshop，对搜出来的对应结果点击安装即可。
安装完成后还需要对插件进行配置，使用快捷键"Command+,"打开设置，然后选择用户设置（User Settings）,点击右边的setting.json进行编辑
![setting_1](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_latex_1.png)
![setting_2](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_latex_2.png)
需要在右边添加如下内容：
```json
{
    "latex-workshop.view.pdf.viewer": "tab",
    "latex-workshop.latex.recipes": [
        {
          "name": "xelatex",
          "tools": [
            "xelatex"
          ]
        },
        {
          "name": "pdflatex -> bibtex -> pdflatex*2",
          "tools": [
            "pdflatex",
            "bibtex",
            "pdflatex",
            "pdflatex"
          ]
        },
        {
          // 编译参考文献
          "name": "xelatex -> bibtex -> xelatex*2",
          "tools": [
            "xelatex",
            "bibtex",
            "xelatex",
            "xelatex"
          ]
        }
      ],
      "latex-workshop.latex.tools": [
        {
          "name": "xelatex",
          "command": "xelatex",
          "args": [
            "-synctex=1",
            "-interaction=nonstopmode",
            "-file-line-error",
            "%DOC%"
          ]
        },
        {
          "name": "latexmk",
          "command": "latexmk",
          "args": [
            "-synctex=1",
            "-interaction=nonstopmode",
            "-file-line-error",
            "%DOC%"
          ]
        },
        {
          "name": "pdflatex",
          "command": "pdflatex",
          "args": [
            "-synctex=1",
            "-interaction=nonstopmode",
            "-file-line-error",
            "%DOC%"
          ]
        },
        {
          "name": "bibtex",
          "command": "bibtex",
          "args": [
            "%DOCFILE%"
          ]
        }
      ],
}
```
### 编写测试
新建一个文本文件，编写内容如下：
```shell
\documentclass{article}
\usepackage{xeCJK}
\setCJKmainfont{STSong}
\begin{document}
hello   中文
\end{document}
```
然后保存并文件名后缀为tex，每次保存就会自动进行编译，当然你也可以点击右边会多出来的一个LaTeX的图标，进行相关的命令操作，如果编译不成功，还可以查看编译日志，找到出错的信息。
![LaTeX_menu](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_latex_3.png)
如果编译没有问题，那么可以点击View LaTeX PDF进行预览。
![pdf_preview](https://cutoutsy-blog-1253675385.cos.ap-chengdu.myqcloud.com/blog_latex_4.png)

### 宏包安装
如果安装的是Basic版本，那么在使用过程中将会遇到手动添加宏包。
宏包的安装管理是使用**tlmgr**来管理的，比如安装ctex包，可以打开命令行运行以下命令：
```shell
sudo tlmgr install ctex
```
在使用过程中，缺少什么宏包就按照这个命令进行安装。

### 编译参考文献
在写文章的过程中，肯定少不了参考文献，而且这也是使用LaTeX的一大优势，不用像使用word那样逐条粘贴。
LaTeX中是使用BibTeX来进行参考文献的处理。BibTeX使用数据库的方式来管理参考文献，BibTeX文件的后缀名为.bib。下面是BibTex文件中的一个参考文献：
```shell
@article{name,
author = {作者, 多个作者用 and 连接},
title = {标题},
journal = {期刊名},
volume = {卷20},
number = {页码},
year = {年份},
abstract = {摘要, 这个主要是引用的时候自己参考的, 这一行不是必须的}
}
```
说明：
> * 第一行@article是参考文献的文章类型。还有其他格式，比如book, conference。
> * name：这个是在正文中应用这个参考文献的名称
> * 其他就是关于这条参考文献的具体内容。

对于上面内容的来源，我们可以选择谷歌学术上的BibTeX格式，复制粘贴到自己的bib文件中。
在tex源文件中我们需要在文档结束前添加
```shell
\bibliography{refer}
\bibliographystyle{plain}
\end{document}
```
bibliography后面是bib文件的文件名，比如我这里是refer.bib。里面内容是：
```shell
@article{yang2017big,
  title={Big Data and cloud computing: innovation opportunities and challenges},
  author={Yang, Chaowei and Huang, Qunying and Li, Zhenlong and Liu, Kai and Hu, Fei},
  journal={International Journal of Digital Earth},
  volume={10},
  number={1},
  pages={13--53},
  year={2017},
  publisher={Taylor \& Francis}
}
```
bibliographystyle这个后面是参考文献的类型，plain是标准类型。其他还有：
> * unsrt: 基本和plain类型一样，参考文献的条目编号是按照引用的顺序，而不是按照作者的字母顺序
> * alpha: 基本和plain类型一样，参考文献的条目编号是基于作者名字和出版年份的顺序
> * abbrv: 缩写格式

在文章中使用\cite{引用文章名称}，引用文章名称就是前面定义的@article后面的名称，比如上面的就是yang2017big。

对于参考文献的编译，我们需要使用编译组合xelatex->bibtex->xelatex->xelatex的方式，这种方式在上面有配置，可以在build中选择这种方式。

### 小结
安装好编写环境后，就可以开心的写文档了。对于LaTeX语法问题，可以在实际中边写边学。

### 参考文献
[Configure Visual Stuido Code as LaTeX IDE](https://ddswhu.me/posts/2018-04/vs-code-for-latex/)
[使用 BibTeX 生成参考文献列表](https://liam.page/2016/01/23/using-bibtex-to-generate-reference/)
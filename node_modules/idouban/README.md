# idouban

一个在网页中嵌入个人豆瓣页面的 javascript 插件。

[![package version](https://badge.fury.io/js/idouban.svg)](https://www.npmjs.com/package/idouban)
[![GitHub license](https://img.shields.io/github/license/mythsman/idouban.svg)](https://github.com/mythsman/idouban/blob/master/LICENSE)

## 配置

```html
<body>
  <div id="douban"></div>
</body>
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/idouban/dist/index.css"
/>
<script
  src="https://cdn.jsdelivr.net/npm/idouban/dist/index.js"
  onload="idouban.init({
      selector:'#douban',
      lang: 'zh',
      douban_id: '162448367',
      type: 'book',
      quote: 'This is my books',
      actions: ['do','wish','collect'],
      page_size: 10,
      max_line: 4
          })"
></script>

```

cdn 缓存可能不是最新，如需最新版本，可直接指定版本号，例如若当前版本是 `1.1.0` ：

* `https://cdn.jsdelivr.net/npm/idouban@1.1.0/dist/index.css`
* `https://cdn.jsdelivr.net/npm/idouban@1.1.0/dist/index.js`

考虑到 jsdelivr 国内访问效果不太好，建议直接 self-host 这些文件。

配置项如下：

* selector : 表示需要将相关代码生成后嵌入到指定 `document.querySelector($selector)` 的元素下。
* lang: 表示国际化的语言，当前支持：zh-cn , zh-tw , en .
* douban_id : 你的豆瓣ID(纯数字格式，不是自定义的域名)。获取方法可以参考[怎样获取豆瓣的数字 ID ？](https://www.zhihu.com/question/19634899)。
* type : 表示需要生成的页面类型，可选项为 `book`, `movie`, `game`, `song`。
* actions: 字符串数组，表示需要展示的"在看"，"想看"，"已看"的顺序，默认是 ["do", "wish", "collect"]。
* quote: 表示需要在页面上方添加的一段引用文本，可不填。
* page_size : 每页需要展示的条目数，默认为10。
* max_line: 条目的元数据最多的行数，超过会以省略号代替，默认为4。

## 原理

插件内部会请求一个豆瓣数据缓存服务 [mouban](https://github.com/mythsman/mouban)，获取响应后直接直接以列表的形式渲染出来。类似功能的插件还有 [hexo-douban](https://github.com/mythsman/hexo-douban)。

用户首次访问时会触发 mouban 的初始化收录。这个过程可能比较久，没有排队的情况下需要等待的时间至少为 条目数/15*5秒 。如果一不小心排队了，则可能需要等待半天。

首次初始化好之后，后续会随着页面的不断访问定时进行增量更新，期间均可正常使用。

## Demo

[读书页](https://blog.mythsman.com/books)
[电影页](https://blog.mythsman.com/movies)
[游戏页](https://blog.mythsman.com/games)
[音乐页](https://mikito.mythsman.com/songs)

## 反馈

系统刚上线，可能还不够完善。如果大家在使用的过程中数据有问题、或者有什么问题和意见，欢迎随时提issue。

如果你觉得这个插件很好用，欢迎右上角点下 star ⭐️，表达对作者的鼓励。

## 免责声明

本项目仅供学习交流使用，不得用于任何商业用
途。

数据来源于互联网公开内容，没有获取任何私有
和有权限的信息（个人信息等），由此引发的任何法律纠纷与本人无关。

---
layout: post
title: "博客搬运笔记"
date: 2020-10-23
tags: ["技术"]
comments: true
author: oneman233
excerpt: "搬家"
toc: true
---

# TODO

* [ ] 博文置顶
* [x] 内置pdf阅读器
* [x] 修改搜索框
* [x] 字体修改
* [ ] 404页面自定义
* [ ] 回到顶部
* [ ] 文章加密

# 博客建立教程

参考：[博客教程](https://lemonchann.github.io/create_blog_with_github_pages/)

# 居中显示图片和说明

```html
<div align=center>
    <img src="图片地址"/>
    <p style="font-size:14px;color:#C0C0C0;text-decoration:underline">
        图片说明
    </p>
</div>
``` 

效果：

<div align=center>
    <p style="font-size:14px;color:#C0C0C0;text-decoration:underline">
        图片说明
    </p>
</div>

# 添加文章摘要和目录

只需要修改文章头部即可：

    ---
    layout: post
    title: "博客搬运笔记"
    date: 2020-10-23
    tags: ["技术"]
    comments: true
    author: oneman233
    excerpt: "搬家"      //在此处攥写文章摘要
    toc: true            //是否开启文章目录
    ---

注意文章的目录会自动识别html文档里的\<h>标签。

并且想要在markdown中直接显示html标签的话需要加上反斜杠转义。

# 显示作者

我fork的这个项目不能更换gitpage主题，于是我到`_layouts`目录下的`post.html`，修改成了下面这样：

```html
<h1>{{ page.title }}</h1>

written by {{ page.author }}
<!-- 显示作者 -->
```

# 嵌入音频

使用 APlayer && MetingJS 实现。

参考：[使用指南](http://yangyingming.com/article/428/)

项目原Github地址：[项目原地址](https://github.com/metowolf/MetingJS#option)

# markdown公式显示不全

参考：[如何采用MathJax](http://leohope.com/%E8%A7%A3%E9%97%AE%E9%A2%98/2017/09/08/page-with-latex/)

# SimpleJekyllSearch报错

报错：`SimpleJekyllSearch --- You must specify a json`

首先参考这一个issue：[issue](https://github.com/christian-fei/Simple-Jekyll-Search/issues/36)，大概看出跟json格式有一定关系

之后参考了这个项目的README：[项目地址](https://github.com/tigerhawkvok/Simple-Jekyll-Search)，里面有一段关于中间件的代码：

```javascript
SimpleJekyllSearch({
  ...
  templateMiddleware: function(prop, value, template){
    if( prop === 'bar' ){
      return value.replace(/^\//, '')
    }
  }
  ...
})
```

仍然没能解决问题，之后参考了一个commit：[commit地址](https://github.com/cse-iitb-wiki/cse-iitb-wiki.github.io/commit/9244aae6a0f450f32c49b4487ac2252dbf0aaae1)，解决的是与我一样的问题，把json格式修改成下面这样：

~~~
---
---
[
  {/% for post in site.posts /%}
   {
     "title"    : "{/% if post.title != "" /%}{{ post.title | escape }}{/% else /%}{{ post.excerpt | strip_html |  escape | strip }}{/% endif /%}",
     "url"      : "{/{ site.baseurl /}}{{ post.url }}",
     "category" : "{/{ post.categories | join: ', '/}}",
     "date"     : "{/{ post.date | date: "/%B /%e, /%Y" /}}"
   } {/% unless forloop.last /%},{/% endunless /%}
  {/% endfor /%}
]
~~~

注意：**注意去掉百分号和大括号前的注释，这里为了防止markdown把它识别为html才注释掉。**

问题仍然存在，最后的解决方案是：**把DOM放在Script之前，这样Script在getElementID的时候才找得到。**

问题解决。

# 文章标题重复

主要是之前写过很多标题为“无题”的文章，虽然在`_post`目录下名字不一样，但是渲染之后的html会重名。

目前的命名方法是在可能重复的标题后面后面再加上发布日期。

# 代码块周围的滚动条

参考：[如何去除不必要的滚动条](https://stackoom.com/question/3k4Ao/%E4%B8%BA%E4%BB%80%E4%B9%88%E6%88%91%E5%9C%A8Jekyll%E7%BD%91%E7%AB%99%E4%B8%8A%E7%9A%84markdown%E4%BB%A3%E7%A0%81%E5%9D%97%E5%91%A8%E5%9B%B4%E5%87%BA%E7%8E%B0%E5%8F%8C%E8%BE%B9%E6%A1%86)

# pdf显示

~~采用插件jekyll-pdf-embed：[项目地址](https://github.com/MihajloNesic/jekyll-pdf-embed)~~

使用以下代码即可：

`\<embed src="文件路径" type="application/pdf" />`

**注意去掉行首的反斜杠。**

效果如下：

<embed src="../files/pdf_test.pdf" type="application/pdf" width="1000" height="500" />
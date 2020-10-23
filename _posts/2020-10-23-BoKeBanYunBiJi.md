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
* [ ] 内置pdf阅读器
* [ ] 修改搜索框
* [ ] 字体修改
* [ ] 404页面自定义

# 博客建立教程

参考：[博客教程](https://lemonchann.github.io/create_blog_with_github_pages/)

# 添加图片和图片说明（居中显示）

    <div align=center>
    <img src="图片地址"/>
    <p style="font-size:14px;color:#C0C0C0;text-decoration:underline">
        图片说明
    </p>
    </div>

效果：

<p style="font-size:14px;color:#C0C0C0;text-decoration:underline">
    图片说明
</p>

# Jekyll添加文章摘要和开启目录

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

# 显示文章作者

我fork的这个项目不能更换gitpage主题，于是我到`_layouts`目录下的`post.html`，修改成了下面这样：

```html
<h1>{{ page.title }}</h1>

written by {{ page.author }}
<!-- 显示作者 -->
```

# 使用 APlayer && MetingJS 嵌入音频

参考：[使用指南](http://yangyingming.com/article/428/)

项目原Github地址：[项目原地址](https://github.com/metowolf/MetingJS#option)

# markdown公式显示不全

参考：[如何采用MathJax](http://leohope.com/%E8%A7%A3%E9%97%AE%E9%A2%98/2017/09/08/page-with-latex/)
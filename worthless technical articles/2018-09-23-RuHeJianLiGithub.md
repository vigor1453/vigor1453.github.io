---
layout: post
title: "如何建立Github"
date: 2018-09-23
tags: ["git"]
comments: true
author: oneman233
excerpt: "从零开始"
toc: true
---

# 基本操作

1. 下载安装git

2. cd到需要上传的本地目录当中去

3. 依次执行以下命令：

```
git init
git add .
git commit -m "（这里写注释）"
git remote add origin（这里写仓库的URL）
git push -u origin master
```

4. 输入账号密码

下一次需要更新github时执行以下命令：
    
```
git clone （这里写仓库的URL）
```

然后找到本地克隆的文件夹，修改结束后执行以下命令：

```
git commit -m "（这里写注释）"
git remote add origin（这里写仓库的URL）
git push -u origin master
```

注意执行`git remote add origin（这里写你的仓库的URL）`出现错误：`fatal: remote origin already exists`，则需要执行以下命令：
    
```
git remote rm origin
```

# 2018.12.5更新两篇

错误`hint: Updates were rejected because the remote contains work that you do`解决方法：

```
git pull origin master --allow-unrelated-histories
git pull origin master
git init
git remote add origin ssh://git@git.limikeji.com:10022/yanhui/webweb.git （可忽略）
git add .
git commit -m 'testst'
git push -u origin master
```

之后删除github 上的文件

# 2019.4.5更新一点

关于在pull requests时出现`adding embedded git repository`错误，大概原因是正在向一个仓库中添加另一个仓库

git要求一个项目只能存放在一个仓库中，不允许仓库的互相嵌套，强行上传会导致文件无法打开，产生原因大概是之前我在不同的地方git init过，解决方案是删除隐藏的.git文件夹再重新git init即可

参考：

[打开隐藏文件](https://jingyan.baidu.com/article/9faa7231f8f570473d28cb78.html)

[简书上关于问题的描述](https://www.jianshu.com/p/49f3c23f54bd)

[CSDN的方案](https://blog.csdn.net/bbtang5568/article/details/82791750?utm_source=blogxgwz6)

# 2019.12.23更新

一些名词的解释：

1. Fork：可以理解为“拉分支”，如果我们对某一个项目比较感兴趣，并且想在此基础之上开发新的功能，这时我们就可以Fork这个项目，这表示复制一个完成相同的项目到我们的 GitHub 账号之中，而且独立于原项目，之后我们就可以在自己复制的项目中进行开发了

2. Pull Request：可以理解为“提交请求”，此功能是建立在Fork之上的，如果我们Fork了一个项目，对其进行了修改，而且感觉修改的还不错，我们就可以对原项目的拥有者提出一个Pull请求，等其对我们的请求审核，并且通过审核之后，就可以把我们修改过的内容合并到原项目之中，这时我们就成了该项目的贡献者

3. Merge：可以理解为“合并”，如果别人Fork了我们的项目，对其进行了修改，并且提出了Pull请求，这时我们就可以对这个Pull请求进行审核。如果这个Pull请求的内容满足我们的要求，并且跟我们原有的项目没有冲突的话，就可以将其合并到我们的项目之中。当然，是否进行合并由我们决定

4. Commit：对repository或者project等做出修改后，都要commit，就是提交修改的意思，可以在commits中查看提交的历史版本

5. Branch：分支从主线中分离出来，做进一步开发时不影响主线。GitHub仓库默认有一个master的主分支，当在开发过程中遇到一个新的需求时，可以新建一个分支同步开发，开发完成后，再合并merge到主分支上

6. Push：此操作目的是把本地仓库传输到到github上，需要输入帐号和密码

# 2019.12.25更新
错误`ERROR: Repository not found`的解决办法：

```
git remote set-url origin git@github.com:xxxxxx/xxxxxx.git
git push
```

# 2020.7.16更新
错误`Updates were rejected because the tip of your current branch is behind`的[解决办法](https://blog.csdn.net/michael10001/article/details/51371715)

错误`custom: description control characters are not allowed`的原因：

传到github上去的时候description里面不能有回车换行符
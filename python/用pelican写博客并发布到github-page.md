Title: 用pelican写博客并发布到github-page
Date: 2017-10-20 12:42
Category: python

>作为程序员写博客记笔记是一件很普遍和有用的事，而我们习惯了md写文档，下面介绍一款用md写博客的工具

## 功能特点 ##

- 多主题选择，可自定义主题
- 保留md
- 纯静态

市面上其实有hexo Jekyll,有相同的功能。为什么我选择pelican? 因为pelicam 基于 *python* 。其他的hexo 基于nodejs，Jekyll貌似是ruby.

## 安装使用 ##

### 安装  ###
```shell
 pip install pelican markdown
 sudo -H pip install pelican markdown ##mac 推荐
```

### 创建项目 ###

```shell
mkdir -p ~/projects/yoursite  ##创建你的项目目录
cd ~/projects/yoursite

pelican-quickstart  ##快速开始命令  完成创建
```

### 写一篇文章 ###

```shell
cd content
code hello-world.md  ## 用vs-code创建md并打开 我设置了cmd启动vscode
```

写入以下内容

```md
Title: hello world
Date: 2010-12-03 10:20
Category: test

hello,world
```

### 生成静态文件 ###

```shell
pelican content
```

### 预览 ###

```
cd output
python -m pelican.server
```

在浏览器上查看 http://localhost:8000/

## 安装主题 ##

可以从[https://github.com/getpelican/pelican-themes](https://github.com/getpelican/pelican-themes) 选择你喜欢的主题

```shell
pelican-themes -l #查看已安装的主题
pelican-themes --install ~/Dev/Python/pelican-themes/notmyidea-cms --verbos ##从该路径安装主题
pelican-themes --remove two-column ##删除主题
pelican-themes --symlink ~/Dev/Python/pelican-themes/two-column ##symlink 安装 很适用于开发主题
```

我fork了官方的一个主题并进行修改地址如下[git@github.com:visonforcoding/attila.git](git@github.com:visonforcoding/attila.git)

## 配置 ##

```python
THEME = "attila"

COLOR_SCHEME_CSS = 'monokai.css'  ##设置代码高亮样式

DISQUS_SITENAME = "your-domain"  ##disqus 域名
```

### 发布到github page ###

首先你需要一个github page repository,具体参考官方文档[https://pages.github.com/](https://pages.github.com/) 写的非常详细

这里主要介绍如何将生产的output 发布到你的github repository,其实官方有提供一个ghp-import,但我觉得与自己想要的并不太一样，因此还是按照自己的方式来。原理就是利用fabric 将output push 到github.修改fabfile.py我的配置如下：

```python
def gh_pages():
    """Publish to GitHub Pages"""
    # rebuild()
    # local("ghp-import -b {github_pages_branch} {deploy_path} -p".format(**env))
    local("pelican content")
    local("cd content && git add . && git commit -m 'update md' && git push origin master && cd ../")
    local("cd output && git add . && git commit -m 'update blog' && git push origin master && cd ../")
```

修改 gh_pages 方法，我这里有2个git commit 和push  ,原因是我想把content 的md提交到github 保存。修改完之后执行fab命令即可将content push到md repository将content push到page repository

```shell
fab gh_pages
```
访问 youdomian.github.io  我的地址 [https://visonforcoding.github.io/](https://visonforcoding.github.io/)

![image.png](http://upload-images.jianshu.io/upload_images/4033700-307543c6b1150521.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





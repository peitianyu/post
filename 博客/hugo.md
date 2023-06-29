---
title: hugo配置
date: 2021-10-19 12:30:12
categories:
  - 博客
tags:
  - 博客
---

### git安装与配置

```shell
# git安装
sudo apt update
sudo apt install git 
#git配置
git config --global user.name "zoey"
git config --global user.email "zoey686@163.com"
#查看设置用户名与密码
git config --list
```

### hugo安装

```shell
# 安装
sudo apt install hugo
# 手动安装
wget https://github.com/gohugoio/hugo/releases/download/v0.54.0/hugo_0.54.0_Linux-64bit.deb
sudo dpkg -i hugo_0.54.0_Linux-64bit.deb
# 查看
hugo --help
hugo version
```

#### 建立博客

新建git文件夹用以git模板等，并在文件夹下新建myblog，用来存放博客页面

```shell
# 新建git文件夹并git初始化
mkdir gitFile  
git init
# 新建myblog文件夹用来存放博客页面
hugo new site myblog
```

myblog下面就会有以下几个文件夹自动生成

|  文件夹名   |          作用          |
| :---------: | :--------------------: |
| archetypes  |      文章开头形式      |
|   content   |          内容          |
|    data     |       自定义模板       |
|   layouts   |      网页模板文件      |
|   static    | 存储图片一些其他的资源 |
|   themes    |          主题          |
| config.toml |        配置文件        |

#### 设置主题

在（https://themes.gohugo.io/）中挑选主题,这里选择的是diary主题（[Diary | Hugo Themes (gohugo.io)](https://themes.gohugo.io/themes/hugo-theme-diary/)）

例子：（[浅蓝色 (gitee.io)](https://sunua.gitee.io/simonblog/)）

```shell
# 下载diary主题到themes文件夹下并重命名为diary
git submodule add https://github.com/AmazingRise/hugo-theme-diary.git themes/diary
# 配置config.toml文件
nano config.toml
```

```
baseURL = "https://peitianyu.github.io/pty_blog.github.io"
DefaultContentLanguage = "zh" # Theme's display language, supports: en, fr, zh, zh-hant
languageCode = "zh-hant"
title = "武装带你"
copyright = "This is a customized copyright."
theme = "diary"

[markup]
  [markup.highlight]
    codeFences = true
    guessSyntax = false
    hl_Lines = ""
    lineNoStart = 1
    lineNos = false
    lineNumbersInTable = true
    noClasses = true
    style = "perldoc"
    tabWidth = 4


enableOpenGraph = true
enableTwitterCards = true
title = "My Blog" 
description = "My HomePage Description"  

[taxonomies]
   ros = "Ros"
   blog = "Blog"

[[menu.main]]
url = "/blog"
name = "Blog"
weight = 1
[[menu.main]]
url = "/ros"
name = "Ros"
weight = 2
```

#### 创建文章

默认主目录文章在content/post下

```
# 创建文章
hugo new post/my_first_blog.md
#创建文章在需要目录(Archive)下
hugo new posts/my_first_blog.md
cd content/post
nano my_first_blog.md
# 注意将draft参数改为false，否则跳过草稿文件，无法预览
```

```
---
title: "my_first_blog"
date: 2021-10-12T10:49:31+08:00
draft : false
---
文本内容
```

本地调试

```
hugo server --theme=blackburn --buildDrafts
```

其中 --theme 选项可以指定主题，–buildDrafts 包括标记为草稿
然后在浏览器里打开： [http://localhost:1313](http://localhost:1313/) 即可访问到你的博客

#### 部署到github

如果你需要部署在 GitHub Pages 上，首先在GitHub上创建一个Repository，命名为：`pty_blog.github.io` （**pty_blog替换为你的github用户名的小写**）

进入仓库，点击**Settings**进入，找到**GitHub Pages**并点击**Check it out here!**，配置**Source**并**save**，会发现绿色框中显现 `Your site is published at https://peitianyu.github.io/pty_blog.github.io/`

此处https://peitianyu.github.io/pty_blog.github.io/就是我们的网址，也是config.toml文件中设置的网址，设置hugo登陆网址

```
hugo --theme=diary --buildDrafts --baseUrl="https://peitianyu.github.io/pty_blog.github.io/"
```

**注意**：这里的 --baseUrl 一定是https://（Hugo中文文档里为http://），不然你部署后的博客会没有样式！

可看到根目录下多出 /public文件夹出来，该文件夹的内容即Hugo生成的整个静态网站。然后继续在你的站点根目录执行git 命令，添加远程仓库。

```shell
cd public
git init
git remote add origin git@github.com:peitianyu/pty_blog.github.io.git
git add -A
git commit -m "first commit"
git push -u origin master
```

然后浏览器里访问：https://peitianyu.github.io/pty_blog.github.io/即可看到刚刚搭建的博客。

发布博客并更新：

```
# 新建博客markdown文件，并编辑博客内容(文件名为 **.md )
hugo new post/newBlog.md
# 生成静态页面
hugo --theme=diary --buildDrafts --baseUrl="https://peitianyu.github.io/pty_blog.github.io/"
# 发布
cd public
git add .
git commit -m "new blog added"
git push origin master
```


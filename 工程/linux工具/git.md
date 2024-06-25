---
title: git使用
date: 2021-10-19 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - tool
---

### 推送新创建git
```shell
# 进入本地文件夹
cd tool
# 添加需要上传文件
echo "# tool" >> README.md
# 初始化git
git init
# 添加需要上传文件
# git add -A 上传全部
git add README.md
# 注释
git commit -m "first commit"
# 添加分支
git branch -M main
# 添加默认远程库
git remote add origin git@github.com:peitianyu/tool.git
# 推送
git push -u origin main
```

### 推送已存在的库

```
git remote add origin git@github.com:peitianyu/tool.git
git branch -M main
git push -u origin main
```

### git删除远程库文件

```shell
git --help # 帮助命令
git pull origin master # 将远程仓库里面的项目拉下来
dir # 查看有哪些文件夹
git rm -r --cached yun_app # 删除yun_app文件夹
git commit -m ‘删除了yun_app’ # 提交,添加操作说明
git push -u origin master # 将本次更改更新到github项目上去
```

### git删除远程分支

```shell
# 快速创建分支并切换分支 (dev 分支)
git checkout -b dev
# 删除分支 ： 如分支名为dev
git branch -d dev 会在删除前检查merge状态（其与上游分支或者与head）。
git branch -D dev 它会直接删除,不检查
# 删除远程分支
git push origin --delete dev
# 清理本地不存在的远程分支，如别人删除了dev,但是你本地查看还有，就可以执行该条命令
git remote prune origin
```

### git 分支

```shell
* 查看分支：git branch
* 创建分支：git branch <name>
* 切换分支：git checkout <name>
* 创建+切换分支：git checkout -b <name>
* 合并某分支到当前分支：git merge <name>
* 删除分支：git branch -d <name>
```

### git clone文件代理

```shell
git clone
git clone https://ghproxy.com/https://github.com/stilleshan/ServerStatus

wget & curl
wget https://ghproxy.com/https://github.com/stilleshan/ServerStatus/archive/master.zip
wget https://ghproxy.com/https://raw.githubusercontent.com/stilleshan/ServerStatus/master/Dockerfile
curl -O https://ghproxy.com/https://github.com/stilleshan/ServerStatus/archive/master.zip
curl -O https://ghproxy.com/https://raw.githubusercontent.com/stilleshan/ServerStatus/master/Dockerfile
```
### 基础使用
```shell
## 新建分支并切换
git branch <branch_name>
git checkout <branch_name>
git add .
git commit -m "commit message"
git push origin <branch_name>
```

### Git报错： Failed to connect to github.com port 443 解决方案
[参考网址](https://blog.csdn.net/zpf1813763637/article/details/128340109)

```
git config --global http.proxy socks5 127.0.0.1:7890
git config --global https.proxy socks5 127.0.0.1:7890

git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890
```

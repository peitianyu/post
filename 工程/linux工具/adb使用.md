---
title: adb使用
date: 2022-4-28 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - tool
---

# 参考网址:

[(129条消息) 全网最全adb命令_autofelix的博客-CSDN博客](https://blog.csdn.net/weixin_41635750/article/details/108297319?ops_request_misc=%7B%22request%5Fid%22%3A%22165113153916780357210551%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165113153916780357210551&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-4-108297319.142^v9^control,157^v4^control&utm_term=adb命令&spm=1018.2226.3001.4187)

# 简单实用

```sh
adb connect "$ip"
adb shell
adb push 本机文件 下机文件夹
adb pull 下机文件 本机文件夹
md5sum 文件 (对比文件是否相同)
```

# 案例
```
adb connect 192.168.10.14
adb shell
adb pull <remote> <local>
adb push <local> <remote>
```


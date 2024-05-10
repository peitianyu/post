---
title: linux解压
date: 2021-11-11 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - linux
---

# 参考网址:

[Linux解压.tgz，.tar.gz文件（tar详细参数使用介绍）_echo-CSDN博客_linux解压.tar.gz文件](https://blog.csdn.net/scanf_linux/article/details/90382491?ops_request_misc=%7B%22request%5Fid%22%3A%22163644518016780271536349%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163644518016780271536349&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-90382491.pc_search_mgc_flag&utm_term=linux解压tar.gz&spm=1018.2226.3001.4187)

# 压缩

```shell
tar -cvf jpg.tar *.jpg //将目录里所有jpg文件打包成jpg.tar 
tar -czf jpg.tar.gz *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar -cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar -Jcvf [目标文件名].tar.xz [原文件名/目录名]
tar -cZf jpg.tar.Z *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg //rar格式的压缩，需要先下载 rar for linux
zip jpg.zip *.jpg //zip格式的压缩，需要先下载 zip for linux
```

# 解压

```shell
tar -xvf file.tar //解压 tar包
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2   //解压 tar.bz2
tar -xZvf file.tar.Z   //解压tar.Z
tar -Jxvf file.tar.xz
unrar e file.rar //解压rar
unzip file.zip //解压zip
```

# 总结

```shell
*.tar 用 tar -xvf 解压
*.gz 用 gzip -d或者gunzip 解压
*.tar.gz和*.tgz 用 tar -xzf 解压
*.bz2 用 bzip2 -d或者用bunzip2 解压
*.tar.bz2用tar -xjf 解压
*.Z 用 uncompress 解压
*.tar.Z 用tar -xZf 解压
*.rar 用 unrar e解压
*.zip 用 unzip 解压
```


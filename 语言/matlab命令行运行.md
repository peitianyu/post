---
title: matlab命令行运行
date: 2022-2-28 12:30:12
categories:
  - 语言
  - matlab
tags:
  - matlab
---

# 参考网址:

 [(110条消息) matlab执行cmd命令是什么意思,cmd 命令行方式执行 matlab 脚本_翻译奥莉姐的博客-CSDN博客](https://blog.csdn.net/weixin_36317943/article/details/115810835?ops_request_misc=%7B%22request%5Fid%22%3A%22164604995316780357295449%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=164604995316780357295449&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-115810835.pc_search_insert_es_download&utm_term=cmd+matlab&spm=1018.2226.3001.4187) 

 [VScode运行MATLAB（最正确版本） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/113231869) 

# cmd窗口运行

```matlab
matlab -nojvm -nodesktop -nodisplay -r test
```

#  远程执行 matlab 脚本(关闭终端) 

```matlab
matlab -nosplash -nodesktop -r test
```

# 插件使用

**matlab extension pack**

**code runner**


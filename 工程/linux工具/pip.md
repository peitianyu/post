---
title: pip
date: 2021-12-11 12:30:12
categories:
  - 工程
  - linux工具
tags:
  - linux
---

# 参考网址:

[(8条消息) pip安装镜像_转载_qq_2711624322-CSDN博客_pip安装镜像](https://blog.csdn.net/weixin_43146445/article/details/85078685?ops_request_misc=%7B%22request%5Fid%22%3A%22162487776816780265445855%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=162487776816780265445855&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-85078685.pc_search_result_control_group&utm_term=pip镜像安装&spm=1018.2226.3001.4187)

[设pip默认镜像源_SimpleLikeMe的博客-CSDN博客](https://blog.csdn.net/SimpleLikeMe/article/details/104480017?ops_request_misc=%7B%22request%5Fid%22%3A%22162761016016780265417243%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=162761016016780265417243&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-104480017.first_rank_v2_pc_rank_v29&utm_term=pip修改默认镜像源&spm=1018.2226.3001.4187)

# 配置

```shell
#临时使用
pip install -i https://mirrors.ustc.edu.cn/pypi/web/simple package
#配置成固定镜像
pip config set global.index-url https://pypi.mirrors.ustc.edu.cn/simple/
```


---
title: io扩展
date: 2021-11-11 12:30:12
categories:
  - 硬件
  - sch
tags:
  - sch

---

# 参考网址:

[IO口不够了？基于I2C的 IO扩展芯片驱动和开发_arenascat的博客-CSDN博客_io扩展芯片](https://blog.csdn.net/u012388993/article/details/106386381?ops_request_misc=%7B%22request%5Fid%22%3A%22163650486016780366540586%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=163650486016780366540586&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_v2~rank_v29-19-106386381.pc_v2_rank_blog_default&utm_term=扩展io&spm=1018.2226.3001.4450)

[单片机扩展IO口_我的博客-CSDN博客](https://blog.csdn.net/zhengqijun_/article/details/53011409?ops_request_misc=%7B%22request%5Fid%22%3A%22163650477816780265471235%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163650477816780265471235&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-53011409.pc_search_mgc_flag&utm_term=扩展io&spm=1018.2226.3001.4187)

# 74HC164(三八译码器),矩阵键盘

这种方式可以实现简单的io扩展,但不可以保持,也就是说,治理只是三位二进制转为八个数,虽然也可以通过加快频率的方式实现.仅用于对时序要求不高的电路,比如led流水灯,而尤其是通讯不太适用的.

#  采用可编程I/O接口扩展芯片

这种方式可以实现io扩展,不过需要一定的编程经验.

采用芯片有 `8255a` 与 `MCP23016`

[i/o扩展芯片_i/o扩展芯片采购信息-立创电子元器件商城 (szlcsc.com)](https://so.szlcsc.com/global.html?k=i%2Fo%E6%89%A9%E5%B1%95%E8%8A%AF%E7%89%87&hot-key=MB85RC16PNF-G-JNERE1)

# 采用A/D获取按键的位置

通过不同电压对应不通按键,挺不错的思路


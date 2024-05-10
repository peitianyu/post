---
title: matlab中文乱码
date: 2022-3-20 12:30:12
categories:
  - 语言
  - matlab
tags:
  - matlab
---

# 参考网址

  [(116条消息) 解决matlab打开.m文件中文注释乱码问题_童童同学的博客-CSDN博客_.m文件中文乱码](https://blog.csdn.net/weixin_45592315/article/details/121953800?spm=1001.2101.3001.6650.7&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-7.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~Rate-7.pc_relevant_default&utm_relevant_index=8) 

# 步骤

- 查看当前matlab编码格式

```matab
slCharacterEncoding()
% 乱码一般显示为:ans =
    'GBK'
```

- 打开matlab/bin文件夹,将 lcdata_utf8.xml 文件复制并重命名为 lcdata.xml ,若存在该文件,将其改名,保存,文件结构与如下

![1647767120_1_.png](https://s2.loli.net/2022/03/20/vwmhXk8UoS2AEGb.png)

- 打开 新复制的lcdata.xml,并删除

```xml
<encoding name=”GBK”>  
  < encoding_alias name=”936”>  
</encoding>
```

- 修改并加入

```xml
修改前
<encoding name=”UTF-8”>  
  <encoding_alias name=”utf8”/> 
</encoding>  
修改后
<encoding name=”UTF-8”>  
  <encoding_alias name=”utf8”/>  
  <encoding_alias name=”GBK”/>  
</encoding>  
```

- 保存并重启matlab
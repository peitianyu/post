---
title: conda
date: 2024-05-17 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - 模板
---

# miniconda
## miniconda安装
    [参考](https://docs.anaconda.com/free/miniconda/)
## 基础使用
```
# 环境管理
conda craete -n test python=3.8 # 创建环境
conda activate test # 激活环境
conda deactivate # 退出环境
conda env list # 查看环境列表
conda env remove -n test # 删除环境

# 包管理
conda search scipy # 搜索scipy
conda install scipy # 安装scipy
conda update scipy # 更新scipy
```
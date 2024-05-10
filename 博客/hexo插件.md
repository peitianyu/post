---
title: hexo插件
date: 2021-5-12 12:30:12
categories:
  - 博客
tags:
  - 博客
---

# 参考网址:

 [(133条消息) hexo博客添加本地搜索功能_玖涯的博客-CSDN博客_hexo 本地搜索](https://blog.csdn.net/nineya_com/article/details/103338696?ops_request_misc=%7B%22request%5Fid%22%3A%22165226309116782395383801%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165226309116782395383801&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-103338696-null-null.142^v9^pc_search_result_cache,157^v4^control&utm_term=hexo本地搜索&spm=1018.2226.3001.4187) 

 [(133条消息) Hexo（sakura）设置文章置顶+私密文章_cungudafa的博客-CSDN博客_hexo隐藏文章](https://blog.csdn.net/cungudafa/article/details/104346521?ops_request_misc=%7B%22request%5Fid%22%3A%22165231909416782395385477%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=165231909416782395385477&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-4-104346521-null-null.142^v9^pc_search_result_cache,157^v4^control&utm_term=hexo隐藏文章&spm=1018.2226.3001.4187) 

# 本地搜索

```sh
# 未配置淘宝的数据源
npm install hexo-generator-json-content --save
#_config.yml中添加
search:
  path: search.xml
  field: post
  content: true
  format: html
# 在_config_butterfly.yml,将local_search,enable改为true
# 本地搜索
local_search:
  enable: true
```

# 私密文章

```yml
# 下载安装encrypt
npm install hexo-blog-encrypt --save
# _config.yml加入
# 文章加密
encrypt:
  enable: true
  abstract: 加密文章
  message: 输入密码，查看文章
# 使用
---
title: hexo插件
date: 2021-5-12 12:30:12
password: 123456
---
```


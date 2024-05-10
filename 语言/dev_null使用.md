---
title: /dev/null使用
date: 2022-5-3 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
hidden: true
---

# 参考网址:

[(130条消息) 关于/dev/null及用途（Linux黑洞）_RayPick的博客-CSDN博客_dev/null](https://blog.csdn.net/weixin_44388689/article/details/120705959?ops_request_misc=&request_id=&biz_id=102&utm_term=/dev/null&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-120705959.142^v9^control,157^v4^control&spm=1018.2226.3001.4187)

# 简介

用来当作垃圾站,不用的东西扔进去,可以用来清理文件内容.

# 简单实例

```cpp
static std:: ofstream dummy_ostream("/dev/null",std::iso::out);
std::ofstream& Logger::operator[](const std::string& name)
	return (m_logs.find(name) == m_logs.end()) ? dummy_ostream : m_logs[names];
```


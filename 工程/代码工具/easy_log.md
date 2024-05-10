---
title: easy_logger
date: 2021-11-26 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - linux
  - cpp
---

# 参考网址:

[一款超轻量级(ROM<1.6K, RAM<0.3k)、高性能的 C/C++ 日志库 ](https://github.com/armink/EasyLogger)

# 移植到linux

[peitianyu/easy_log (github.com)](https://github.com/peitianyu/easy_log)

注意修改log文件保存路径,在`easy_log/include/elog_file_cfg.h`中

一款更加轻量化的log工具,可以自行更改函数,实现功能

[ peitianyu/light_log (github.com)](https://github.com/peitianyu/light_log/blob/main/README.md)

```cc
#include <fstream>
#include <stdarg.h>
class Logger
{
public:
    struct Options
    {
        std::string file_name = "log.txt";
    };
public:
    Logger(const Options &options): m_options(options)
    {
        m_file.open(m_options.file_name, std::ios::out);
    }
    ~Logger()
    {
        m_file.close();
    }
    void LOG(const char *file, int line, const char *func, const char *fmt, ...)
    {
        va_list args;
        va_start(args, fmt);
        char buf[1024];
        vsnprintf(buf, sizeof(buf), fmt, args);
        va_end(args);
        // file只保留文件名
        std::string file_name = file;
        file_name = file_name.substr(file_name.find_last_of("/") + 1);
        m_file << file_name << ":" << line << ":" << func << ": " << buf << std::endl;
    }

protected:
    Options m_options;
    std::ofstream m_file;
};
#define LOG_INFO(fmt, ...) Logger::LOG(__FILE__, __LINE__, __FUNCTION__, fmt, ##__VA_ARGS__)

int main()
{
    Logger logger(Logger::Options{});
    logger.LOG_INFO("%s", "hello world");

    return 0;
}
```


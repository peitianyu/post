---
title: cjson精度丢失
date: 2021-11-24 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - cpp
---

# 参考网址:

[(80条消息) cJSON精度丢失问题_weixin_33858485的博客-CSDN博客](https://blog.csdn.net/weixin_33858485/article/details/93953963?ops_request_misc=&request_id=&biz_id=102&utm_term=cjson保留两位小数&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-6-93953963.pc_search_mgc_flag&spm=1018.2226.3001.4187)

在`cJSON.c`中的`print_number`函数中的`sprintf((char*)number_buffer, "%1.17g", d);`更改为

`sprintf((char*)number_buffer, "%1.4g", d);`

```c
/* Render the number nicely from the given item into a string. */
static cJSON_bool print_number(const cJSON * const item, printbuffer * const output_buffer)
{
    unsigned char *output_pointer = NULL;
    double d = item->valuedouble;
    int length = 0;
    size_t i = 0;
    unsigned char number_buffer[26] = {0}; /* temporary buffer to print the number into */
    unsigned char decimal_point = get_decimal_point();
    double test = 0.0;

    if (output_buffer == NULL)
    {
        return false;
    }

    /* This checks for NaN and Infinity */
    if (isnan(d) || isinf(d))
    {
        length = sprintf((char*)number_buffer, "null");
    }
    else
    {
        /* Try 15 decimal places of precision to avoid nonsignificant nonzero digits */
        // 更改这里的1.4,这里指的是保留4位小数
        length = sprintf((char*)number_buffer, "%1.4g", d);

        /* Check whether the original double can be recovered */
        if ((sscanf((char*)number_buffer, "%lg", &test) != 1) || !compare_double((double)test, d))
        {
            /* If not, print with 17 decimal places of precision */
            // 更改这里的1.4,这里指的是保留4位小数
            length = sprintf((char*)number_buffer, "%1.4g", d);
        }
    }

    /* sprintf failed or buffer overrun occurred */
    if ((length < 0) || (length > (int)(sizeof(number_buffer) - 1)))
    {
        return false;
    }

    /* reserve appropriate space in the output */
    output_pointer = ensure(output_buffer, (size_t)length + sizeof(""));
    if (output_pointer == NULL)
    {
        return false;
    }

    /* copy the printed number to the output and replace locale
     * dependent decimal point with '.' */
    for (i = 0; i < ((size_t)length); i++)
    {
        if (number_buffer[i] == decimal_point)
        {
            output_pointer[i] = '.';
            continue;
        }

        output_pointer[i] = number_buffer[i];
    }
    output_pointer[i] = '\0';

    output_buffer->offset += (size_t)length;

    return true;
}
```


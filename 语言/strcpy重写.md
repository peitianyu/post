---
title: strcpy重写
date: 2021-11-25 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

[什么？面试官让我写strcpy()函数_m0_57304511的博客-CSDN博客](https://blog.csdn.net/m0_57304511/article/details/121503898)

# strpy

```c++
#include<stdio.h>
#include<assert.h>
char * my_strcpy(char* dest, const char* src)
//const可以防止源字符串被改变
{
    // 使用断言避免传入空指针
	assert( dest && src );
	char* ret = dest;
    // 按地址复制,直到为空
	while(*dest++ = *src++)
	{
		;
	}
	return ret;
}
int main()
{
	char arr1[20] = { 0 };
	char arr2[] = "hello";
 
	printf("%s\n", my_strcpy(arr1, arr2));
	return 0;
}
```

# mempy

```c++
void * memcpy(void *dest, const void *src, unsigned int count);  
{  
    if ((src == NULL) || (dest == NULL))  
        return;  
      
    while (count--)  
        *dest++ = *src++;  
          
    return dest;  
}
```


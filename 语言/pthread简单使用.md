---
title: pthread简单使用
date: 2021-12-17 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

[pthread 线程基本函数_chenwh_cn的博客-CSDN博客_pthread函数](https://blog.csdn.net/chenwh_cn/article/details/117225053?ops_request_misc=%7B%22request%5Fid%22%3A%22163970172716780265411237%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163970172716780265411237&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-3-117225053.pc_search_em_sort&utm_term=pthread&spm=1018.2226.3001.4187)

[Pthread线程基础学习_我的梦-CSDN博客_pthread教程](https://blog.csdn.net/u013457167/article/details/83691039?ops_request_misc=%7B%22request%5Fid%22%3A%22163970172716780265411237%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163970172716780265411237&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-7-83691039.pc_search_em_sort&utm_term=pthread&spm=1018.2226.3001.4187)

# 创建线程

```cpp
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

void *thread_fun(void* arg)
{
    int num = *((int*)arg);
    printf("int the new thread: num = %d\n", num);
}

int main(int argc, char *argv[])
{
    pthread_t tid;
    int test = 100;

    pthread_create(&tid, NULL, thread_fun, (void *)&test);
    while(1);
    return 0;
}
```

# 线程等待

```cpp
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

void *thead_fun(void *arg)
{
    static int ret = 5; // thread_join.cpp:7:13: warning: address of local variable ‘ret’ returned [-Wreturn-local-addr]
    sleep(1);
    pthread_exit((void*)&ret);
    //return (void*)&ret;
}
// 等待线程结束并回收线程资源，此函数会阻塞，类似进程的 wait() 函数。如果线程已经结束，那么该函数会立即返回
int main(int argc, char *argv[])
{
    pthread_t tid;
    void *ret = NULL;

    pthread_create(&tid, NULL, thead_fun, NULL);
    pthread_join(tid, &ret);
    printf("ret = %d\n", *((int*)ret));

    return 0;
}
```

# 分离线程

```cpp
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

void *thead_fun(void *arg)
{
    for(int i=0; i<3; i++)
    {
        printf("thread is runing\n");
        sleep(1);
    }
    return NULL;
}

int main(int argc, char *argv[])
{
    pthread_t tid;

    pthread_create(&tid, NULL, thead_fun, NULL);
    pthread_detach(tid);
    if (pthread_join(tid, NULL)) // 立即返回，调用失败
    {
        printf("join not working\n");
    }
    printf("after join\n");
    sleep(5);
    printf("master is leaving\n");
    return 0;
}
```

# 退出线程

```cpp
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>

void *thead_fun(void *arg)
{
    static int ret = 5; // thread_join.cpp:7:13: warning: address of local variable ‘ret’ returned [-Wreturn-local-addr]
    sleep(1);
    pthread_exit((void*)&ret);
    //return (void*)&ret;
}

int main(int argc, char *argv[])
{
    pthread_t tid;
    void *ret = NULL;

    pthread_create(&tid, NULL, thead_fun, NULL);
    pthread_join(tid, &ret);
    printf("ret = %d\n", *((int*)ret));

    return 0;
}
```

# 取消线程

```cpp
#include<stdio.h>
#include<stdlib.h>
#include <pthread.h>
void *thread_fun(void *arg)
{
	int i=1;
	printf("thread start \n");
	while(1)
	{
		i++;
        // 调用pthread_testcancel，让内核去检测是否需要取消当前线程。
		pthread_testcancel();
	}
	return (void *)0;
}
int main()
{
	void *ret=NULL;
	int iret=0;
	pthread_t tid;
	pthread_create(&tid,NULL,thread_fun,NULL);
	sleep(1);
	
	pthread_cancel(tid);//取消线程
	pthread_join(tid, &ret);
	printf("thread 3 exit code %d\n", (int)ret);
	
	return 0;
	
}
```


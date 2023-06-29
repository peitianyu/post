---
title: pthread_mutex使用
date: 2021-12-17 12:30:12
categories:
  - 语言
  - cpp
tags:
  - cpp
---

# 参考网址:

[pthread详解_networkhunter的博客-CSDN博客_phread](https://blog.csdn.net/networkhunter/article/details/100218945?ops_request_misc=%7B%22request%5Fid%22%3A%22163970172716780265411237%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163970172716780265411237&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-2-100218945.pc_search_em_sort&utm_term=pthread&spm=1018.2226.3001.4187)

[关于pthread_mutex_lock使用_无专精则不能成，无涉猎则不能通-CSDN博客_pthread_mutex_timedlock](https://blog.csdn.net/z_muyangren/article/details/105398871?ops_request_misc=%7B%22request%5Fid%22%3A%22163970180516780271917732%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163970180516780271917732&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-1-105398871.pc_search_em_sort&utm_term=pthread_mutex_lock&spm=1018.2226.3001.4187)

[linux读写锁 pthread_rwlock/互斥锁pthread_mutex_三眼二郎-CSDN博客_pthread_mutex_t pthread_rwlock_t](https://blog.csdn.net/a6333230/article/details/113654694?ops_request_misc=%7B%22request%5Fid%22%3A%22163970180516780264024377%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=163970180516780264024377&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-2-113654694.pc_search_em_sort&utm_term=pthread_mutex_lock&spm=1018.2226.3001.4187)

# 常用pthread_mutex_lock

```cpp
#include <pthread.h>

int pthread_mutex_destroy(pthread_mutex_t *mutex);
int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr);
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);
int pthread_mutex_timedlock(pthread_mutex_t *restrict mutex, const struct timespec *restrict abs_timeout);
```

# 使用

```cpp
// 创建
pthread_mutex_t mtx;
// 初始化互斥量
// 第二个参数为 NULL，互斥锁的属性会设置为默认属性
pthread_mutex_init(&mtx, NULL);
// 阻塞调用
pthread_mutex_lock(&mtx);
// 非堵塞调用
int err = pthread_mutex_trylock(&mtx);
if(0 != err) {
    if(EBUSY == err) {
        //The mutex could not be acquired because it was already locked.
    }
}
// 超时调用
struct timespec abs_timeout;
abs_timeout.tv_sec = time(NULL) + 1;
abs_timeout.tv_nsec = 0;

int err = pthread_mutex_timedlock(&mtx, &abs_timeout);
if(0 != err) {
    if(ETIMEDOUT == err) {
        //The mutex could not be locked before the specified timeout expired.
    }
}
// 释放互斥锁
pthread_mutex_unlock(&mtx);
// 销毁线程锁
pthread_mutex_destroy(&mtx)
```

# 简单使用

```cpp
#include<pthread.h>
#include<unistd.h>
#include<stdio.h>
#include<stdlib.h>
 
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;/*初始化互斥锁*/
pthread_cond_t  cond = PTHREAD_COND_INITIALIZER;//init cond
 
void *thread1(void*);
void *thread2(void*);
 
int i = 1; //global
 
int main(void){
    pthread_t t_a;
    pthread_t t_b;//two thread
 
    pthread_create(&t_a,NULL,thread2,(void*)NULL);
    pthread_create(&t_b,NULL,thread1,(void*)NULL);//Create thread
    
    printf("t_a:0x%x, t_b:0x%x:", t_a, t_b);
    pthread_join(t_b,NULL);//wait a_b thread end
    pthread_mutex_destroy(&mutex);
    pthread_cond_destroy(&cond);
   exit(0);
}
 
void *thread1(void *junk){
    for(i = 1;i<= 9; i++){
        pthread_mutex_lock(&mutex); //互斥锁
        printf("call thread1 \n");
        if(i%3 == 0)
        	{
                pthread_cond_signal(&cond); //send sianal to t_b
                printf("thread1:******i=%d\n", i);
        	}
        else
            printf("thread1: %d\n",i);
        pthread_mutex_unlock(&mutex);
 
		printf("thread1: sleep i=%d\n", i);
        sleep(1);
		printf("thread1: sleep i=%d******end\n", i);
    }
}
 
void *thread2(void*junk){
    while(i < 9)
    {
        pthread_mutex_lock(&mutex);
        printf("call thread2 \n");
        if(i%3 != 0)
            pthread_cond_wait(&cond,&mutex); //wait
        printf("thread2: %d\n",i);
        pthread_mutex_unlock(&mutex);
 
		printf("thread2: sleep i=%d\n", i);
        sleep(1);
		printf("thread2: sleep i=%d******end\n", i);		
    }
}      
```


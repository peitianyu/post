---
title: shm
date: 2021-11-23 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - cpp
---

# 参考网址:

[进程间通信——共享内存（Shared Memory）_ypt-CSDN博客_共享内存](https://blog.csdn.net/ypt523/article/details/79958188?ops_request_misc=%7B%22request%5Fid%22%3A%22163767553616780274114023%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163767553616780274114023&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-79958188.pc_search_mgc_flag&utm_term=共享内存+进程间通信&spm=1018.2226.3001.4187)

# 例程

```c
//comm.h
#ifndef _COMM_H__
#define _COMM_H__
 
#include<stdio.h>
#include<sys/types.h>
#include<sys/ipc.h>
#include<sys/shm.h>
 
#define PATHNAME "."
#define PROJ_ID 0x6666
 
int CreateShm(int size);
int DestroyShm(int shmid);
int GetShm(int size);
#endif
```

```c
//comm.c
#include"comm.h"
 
static int CommShm(int size,int flags)
{
	key_t key = ftok(PATHNAME,PROJ_ID);
	if(key < 0)
	{
		perror("ftok");
		return -1;
	}
	int shmid = 0;
	if((shmid = shmget(key,size,flags)) < 0)
	{
		perror("shmget");
		return -2;
	}
	return shmid;
}
int DestroyShm(int shmid)
{
	if(shmctl(shmid,IPC_RMID,NULL) < 0)
	{
		perror("shmctl");
		return -1;
	}
	return 0;
}
int CreateShm(int size)
{
	return CommShm(size,IPC_CREAT | IPC_EXCL | 0666);
}
int GetShm(int size)
{
	return CommShm(size,IPC_CREAT);
}
```

```c
//client.c
#include"comm.h"
 
int main()
{
	// 获得共享内存的id
	int shmid = GetShm(4096);
	sleep(1);
	// 将共享内存映射到本进程的地址空间
	char *addr = shmat(shmid,NULL,0);
	sleep(2);
	int i = 0;
	while(i < 26)
	{
		addr[i] = 'A' + i;
		i++;
		addr[i] = 0;
		sleep(1);
	}
	// 将共享内存从本进程的地址空间中卸载
	shmdt(addr);
	sleep(2);
	return 0;
}
```

```c
//server.c
#include"comm.h"
 
int main()
{
	// 创建并获得共享内存的id
	int shmid = CreateShm(4096);
	// 将共享内存映射到本进程的地址空间
	char *addr = shmat(shmid,NULL,0);
	sleep(2);
	int i = 0;
	while(i++ < 26)
	{
		printf("client# %s\n",addr);
		sleep(1);
	}
	// 将共享内存从本进程的地址空间中卸载
	shmdt(addr);
	sleep(2);
	// 删除共享内存
	DestroyShm(shmid);
	return 0;
}
```

```makefile
//Makefile
.PHONY:all
all:server client
 
client:client.c comm.c
	gcc -o $@ $^
server:server.c comm.c
	gcc -o $@ $^
 
.PHONY:clean
clean:
	rm -f client server
```


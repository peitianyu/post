---
title: Socket编程
date: 2021-10-20 12:30:12
categories:
  - 工程
  - 代码工具
tags:
  - cpp
---

### 参考网址：

[peitianyu/TcpIpBook: TCP/IP网络编程 (github.com)](https://github.com/peitianyu/TcpIpBook)

[TCP/IP网络通信之Socket编程入门_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1eg411G7pW?from=search&seid=9546660162554024003&spm_id_from=333.337.0.0)

### 套接字操作

```
#include<sys/socket.h>
# 创建套接字
int socket(int domain, int type, int protocol);
//成功时返回文件描述符，失败时返回-1
# 分配地址信息
int bind(int sockfd, struct sockaddr* pSockAddr, socklen_t addrLen);
//成功时返回0， 失败时返回-1
# 设置监听
int listen(int sockfd, int backlog);
//成功时返回0， 失败时返回-1
# 受理连接
int accept(int sockfd, struct sockaddr* pSockAddr, socklen_t* pAddrLen);
//成功时返回文件描述符，失败时返回-1
# 请求连接
int connect(in sockfd, struct sockaddr* pSockAddr, socklen_t sockLen);
//成功时返回0， 失败时返回-1
```

### 文件操作

```
# 打开文件
#include<fcntl.h>
int open(const char* path, int flag);
//成功时返回文件描述符， 失败时返回-1
# 关闭文件
#include<unistd.h>
int close(int fd);
//成功时返回0， 失败时返回-1
# 写入文件
#include<unistd.h>
ssize_t write(int sockfd, const void* buf, size_t nBytes);
//成功时返回写入的字节数，失败时返回-1
# 读取文件
ssize_t read(int sockfd, void* buf, size_t nBytes);
//成功时返回读到的字节数（若遇到文件结尾则返回0），失败时返回-1

#include<sys/uio.h>
# 写文件
ssize_t writev(int sockfd, const struct iovec* iov, int iovcnt);
//成功时返回发送的字节数，失败时返回-1
# 读文件
ssize_t readv(int sockfd, const struct iovec* iov, int iovcnt);
//成功时返回发送的字节数，失败时返回-1

// TCP
// 发送文件
ssize_t send(int sockfd, const void* buf, size_t nbytes, int flags);
//成功时返回发送的字节数，失败时返回-1
// 接收文件
ssize_t recv(int sockfd, const void* buf, size_t nbytes, int flags);
//成功时返回接收到的字节数（收到EOF时返回0），失败时返回-1
// UDP接收发送
ssize_t sendto(int sockfd, const void *buff, size_t nbytes, int flags,
	           const struct sockaddr *to, socklen_t *addrlen);
//成功时返回发送的字节数，失败时返回-1,参数flags 一般设0
ssize_t recvfrom(int sockfd, const void *buff, size_t nbytes, int flags,
	           const struct sockaddr *to, socklen_t *addrlen);
//成功时返回接收的字节数，失败时返回-1,参数flags 一般设0
struct iovec
{
    void* iov_base; //缓冲地址
    size_t iov_len; //缓冲大小
}

# 断开连接
int shutdown(int sock, int howto);
//成功时返回0，失败时返回-1
```

### 套接字的可选项和IO缓冲大小

```
#include<sys/socket.h>
int getsockopt(int sock, int level, int optname,void* optval,socklen_t* optlen);
//成功时返回0，失败时返回-1
int setsockopt(int sock, int level, int optname, void* optval,socklen_t optlen);
//成功时返回0，失败时返回-1
```

```
int sock = socket(PF_INET,SOCK_STREAM,0);
int optval;
socklen_t optlen;
int state = getsockopt(sock, SOL_SOCKET, SO_SNDBUF,(void*)&optval, &optlen);

optval = 1024*3;
optlen = sizeof(optval);
state = setsockopt(sock, SOL_SOCKET, SO_RCVBUF,(void*)&optval,optlen);
```

### IO复用

- `FD_ZERO(fd_set* fdset)`: 将fdset变量的所有位初始化位0
- `FD_SET(int fd, fd_set* fdset)`: 向fdset变量中注册文件描述符fd
- `FD_CLR(int fd, fd_set* fdset)`: 清除fdset变量中的文件描述符fd
- `FD_ISSET(int fd, fd_set* fdset)`: fdset变量中是否存在fd文件描述符

```
# 设置监视范围及超时
#include<sys/select.h>
#include<sys/time.h>

int select(int maxfd, fd_set* readset, 
            fd_set* writeset,fd_set* exceptset, 
            const struct timeval* timeout
            );
//失败时返回-1，超时返回0，成功时返回发生事件的文件描述符数量

struct timeval
{
    long tv_sec;
    long tv_usec;
}
```

- maxfd: 监视的文件描述符数量（最大的文件描述符+1）
- readset: 将关注“是否存在待读取数据”的文件描述符存放在readset变量中
- writeset: 将关注”是否可写“的文件描述符存放在writeset变量中
- exceptset: 将关注”是否有异常“的文件描述符放在exceptset变量中 -timeout: 为了防止调用select函数后陷入无限阻塞状态，可以传递超时信息


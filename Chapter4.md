1.socket  函数

```c
#include <sys/socket.h>
int socket(int family, int type, int protocal);
```

2.connect  函数

```c
#include <sys/socket.h>
int connect(int sockfd, const struct sockaddr *servaddr, socklen_t addrlen);
```

- 如果是 TCP 套接字， connect 函数激发 tcp 三次握手, 错误类型：

  - ETIMEOUT tcp 客户没有收到来自主机的 syn回复
  - RST 表示主机对应端口没有进程等待与之链接
  - EHOSTUNREACH 软错误， 可能是目的不可达

  

3.bind  函数

```c
#include <sys/socket.h>
int bind(int sockfd, const struct sockaddr *myaddr, socklen_t addrlen);
```

- 第二个参数是一个指向特定于协议的地址结构的指针，绑定时端口号是可选的
- 对客户端，指定ip地址成为唯一源地址，对服务器只会接受该ip地址的数据包

4.listen  函数

```c
#include <sys/socket.h>
int listen(int sockfd, int backlog);
```

- listen 将一个未连接的套接字转化成一个被动套接字，只是内核接受该套接字的连接请求
- backlog 参数制定了相应套接字排队的最大连接个数
  - 未完成队列
  - 已完成队列
  - 两队列之和不超过backlog
- 当客户发来syn,但此时队列已满，忽略该报文，目的是因为队列满状态是暂时的

5.accept 函数

```c
#include <sys/socket.h>
int accept(int sockfd, struct sockaddr *cliaddr, socklen_t addr_len);
```

- sockfd -- 监听套接字， 如果建立连接  返回 已连接套接字

6.fork  exec  函数

```c
#include <unistd.h>

pid_t fork(void);

int exec1(const char *pathName, const char *arg0, ...);

/* exec1 execv execle execve execlp exvp */
```


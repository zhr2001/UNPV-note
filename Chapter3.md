1.套接字结构：

```c
struct sockaddr_in {
    uint8_t sin_len;
    sa_family_t sin_family;
    in_port_t sin_port;
    
    struct in_addr sin_addr;
    
    char sin_zero[8];
}

struct sockaddr {
    uint8_t sa_len;
    sa_family_t sa_family;
    char sa_data[14];
}
```

- 将特定协议的套接字接口转化成通用套接字结构指针

2.地址转换 

```c
#include <arpa/inet.h>

int inet_aton(const char *strptr, struct in_addr *addrptr);
/* 字符串有效返回 1 ， 否则返回 0 */

in_addr_t inet_addr(const char *strptr) ;
/* 字符串有效返回 32 位二进制网络字节序的 ipv4 地址， 否则返回 INADDR_NONE */

char *inet_ntoa(struct in_addr_t inaddr);
/* 与 inet_aton 函数效果相反 */

int inet_pton(int family, char *strptr, void *addrptr); 

const char *inet_ntop(int family, const void *addrptr, char *strptr, size_t len);



#include "unp.h"

char *sock_ntop(const struct sockaddr *sockaddr, socklen_t addrlen);
```

- len 参数防止内存溢出， 如果 len 太小返回空指针
- inet_pton, inet_ntop 函数都是针对通用套接字， ipv4/ipv6 都可以
- sock_ntop 函数查看结构体内部，然后以适当函数返回该地址的表达格式， 该函数不可重入且非线程安全

3.readn, writen, readline函数


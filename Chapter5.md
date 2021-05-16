# TCP回射客户程序

### 1.编译命令

```cmd
gcc /* filename */  -lunp -o  /* target name */
```

### 2.回射函数

```c
#include "unp.h"

void 
str_echo(int sockfd)
{
    ssize_t n;
    char buf[MAXLINE];
    
again:
    while ((n = read(sockfd, buf, MAXLINE)) > 0) 
        Writen(sockfd, buf, n);
        
    if (n > 0 && errno == EINTR)
       goto again;
    else if (n < 0)
        err_sys("str_echo: read error.");
}
```

`

### 3.客户端状态变化

- FIN_WAIT_2 	->	TIME_WAIT	-> 	客户端子进程发送  SIGCHLD  信号
- 发送  SIGCHLD  信号   行为被父进程忽略， 直接导致子进程成为僵死进程



### 4.POSIX信号处理

- 信号由一个进程发给另一个进程
- 由内核发给某个进程

```c
/* 信号捕获程序  KILL STOP 信号不能被捕获*/
void handler(int signo);

/* 信号处置函数 */
Sigfunc *
signal(int signo, Sigfunc *func)
{
 	struct sigaction act, oact;
    
    act.sa_handler = func;
    sigemptyset(&act.sa_mask);
    act.sa_flags = 0;
    if (signo == SIGALRM) {
#ifdef SA_INTERRUPT
        act.sa_flags |= SA_INTERRUPT;
#endif
    } else {
#ifdef SA_RESTART
        act.sa_flags |= SA_RETART;
#endif
    }
    if (sigaction(signo, &act, &oact) < 0)
        return SIGERR;
    return (oact.sa_handler);
}
```

- 信号处理函数与对应信号长时间绑定
- 在一个信号处理函数运行期间，正被递交的信号是阻塞的，sa_mask指定的额外信号也是阻塞的
- 如果一个信号在阻塞期间产生了一次或者多次，那么该信号被解阻塞之后通常只递交一次，默认不排队
- sigprocmask 函数可以阻塞某组信号，从而保护函数

### 5.处理SIGCHLD信号

 略略略

### 6处理被中断的系统调用

- accept   慢系统调用 -- always 阻塞
- connect 系统调用不能重启

### 7.wait, waitpid

- 当客户同时与服务器五个进程通信，wait 不会处理多余的  SIG_CHLD 信号，导致出现除第一个之外其余所有的进程全部变成僵死进程
- waitpid(-1, &stat, WNOHANG) 告诉 waitpid 不要阻塞信号

### 8.accept返回之前终止

三次握手之后，客户 tcp 发送 rst 复位， 服务器调用 accept

### 9.服务器进程终止

1. kill 服务器进程，服务器进程向客户端发送 fin , 客户端此时被阻塞与 fgets()
2. 键入文本之后，服务器接受到该文本，但该进程已经终止，于是响应一个 rst 
3. 用户进程看不见这个 rst , readline 返回 0， 提示服务端过早终止

### 10.SIGPIPE信号

### 11.服务器主机崩溃

- 客户TCP持续重传数据分节，试图从服务器上接收一个ACK，等待大约9分钟返回ETIMEOUT   /   DESTINATION UNREACHABLE
- 若崩溃后重启，由于服务器失去了原本的TCP连接，所以对客户端的数据相应一个 RST

### 12.数据格式

- 字符串

  sscanf 将文本串中两个参数转化成长整数，调用snpintf把结果转化成文本串

  ```c
  /* 将两个数求和 */
  #include "unp.h"
  
  int 
  str_echo(int argc, int **argv)
  {
      long arg1, arg2;
      ssize_t n;
      char line[MAXLINE];
      
      for ( ; ; ) {
          if ((n == Readline(sockfd, line, MAXLINE)) == 0)
              return ;
          if (sscanf(line, "%ld%ld", &arg1, &arg2) == 2) 
              sprintf(line, sizeof(line), arg1 + arg2);
          else 
              sprintf(line, sizeof(line), "input error !\n");
          n = strlen(line);
          Writen(sockfd, line, n);
      }
  }
  ```

  


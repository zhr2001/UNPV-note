# Chapter6 select, poll, IO复用

### 1.阻塞式IO

### 2.非阻塞IO

### 3.IO复用

- select 优势 -- 等待多个文件描述符就绪

### 4.信号驱动式IO模型

### 5.异步IO

我们调用aio_read函数给内核传递文件描述符、缓冲区指针、缓冲区大小和文件偏移，并告诉内核当整个操作完成时如何通知我们。系统调用立即返回，进程不会阻塞。

- 与信号驱动式区别：

  信号驱动在数据准备好之后，内核向进程发信号，异步IO是将数据报复制到缓冲区之后返回信号

### 6.select函数

- 该函数允许指示内核等待多个事件中的任何一个发生，并只在有一个或多个事件发生或经历一段指定的时间后才唤醒它

  ```c
  #include <sys/select.h>
  #include <sys/time.h>
  
  int select(int maxfdp1, fd_set *readset, fd_set *writeset, fd_set *exceptset,
            const struct timeval *timeout);
  /* 若有就绪描述符返回其数量，若超时返回0，若出错返回-1 */
  ```

  - timeout  -- 告诉内核就绪可花多长时间

  - readset,writeset,exceptset --- 内核测试读写异常描述符

    ```c
    /* 初始化描述符 */
    fd_set rset;
    
    FD_ZERO(&rset);
    FD_SET(1, &rset);
    ```

  - maxfdp1  ---只想待测试的描述符个数

### 7.重写str_cli函数

- 将fgets阻塞修改为select阻塞
- 混合使用 stdio 与 select 是错误的，因为select不知道stdio使用了缓冲区,它不管stdio的缓冲区还有待消费的输入

### 8.shutdown与close

- close函数的两个作用：

  - 把描述符的引用减去1
  - close终止读和写两个方向上的传递

- ```c
  #include <sys/socket.h>
  
  int shutdown(int sockfd, int howto);
  ```

- shutdown函数行为取决于howto参数

  - SHUT_RD  关闭连接的读这一半
  - SHUT_WR 关闭连接的写这一半
  - SHUT_RDWR 全部关闭


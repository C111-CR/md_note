## epoll

[关于Linux的epoll机制的介绍](https://zhuanlan.zhihu.com/p/393747291)

头文件: `#include <sys/epoll.h>`

- ##### 创建内核事件表

  ```c++
  int epoll_create(int size);
  ```

​		`size` 参数给内核一个提示，告诉它事件表需要多大。

​		函数返回一个文件描述符，标识一个内核中的事件表

- ##### 操作事件表

  ```c++
  int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
  ```

  `epfd` --- 前面用`epoll_create`创建的文件标识符

  `fd` --- 要操作的事件的文件标识符

  `op` --- 指定操作类型

  - `EPOLL_CTL_ADD`：往事件表中注册fd上的事件
  - `EPOLL_CTL_MOD`：修改fd上的注册事件
  - `EPOLL_CTL_DEL`：删除fd上的注册事件

  `event` --- 指定事件

  - ```c++
    struct epoll_event{
        _uint32_t events;	// epoll事件标识符
        epoll_data_t data;	// 用户数据
    };
    ```

  - ```c++
    typedef union epoll_data{
        void* ptr;
        int fd;
        uint32_t u32;
        uint64_t u64;
    }epoll_data_t;
    ```

  函数成功时返回0， 失败时返回-1并设置`errno` 

- ##### `epoll_wait()` 

  ```c++
  int epoll_wait( int epfd, struct epoll_event* events, int maxevents, 
                  int timeout );
  ```

  `timeout` --- 超时值（单位：ms）

  - 当值为-1时，`epoll_wait()`调用永远阻塞，直到某个事件发生。
  - 当值为0时，调用将立即返回。
  - 当值大于0时，表示在`epoll_wait()`会在这段超时值内等待事件...

  `maxevents` --- 最多监听多少事件，必须大于0

  `epfd` --- 用`epoll_create`创建的文件标识符

  `events` --- 事件数组，将若干个事件相与得到

- ##### 两种读写触发模式

  - ##### LT模式：电平触发（默认）（Level Trigger）

    ###### 对于LT模式，当`epoll_wait`检测到有事件发生并将此事件通知应用程序后，程序可以不立即处理该事件。这样当下一次应用调用`epoll_wait`时，此事件会被再次报告给应用程序，重复此过程直到事件被处理。因此，LT模式实际上相当于一个效率较高的`poll`（poll采用轮询方式查找就绪事件）。
    
  - ##### ET模式：边沿触发（Edge Trigger）
  
    ###### 对于ET模式，当`epoll_wait`检测到有事件发生并将此事件通知应用程序后，程序必须立即处理该事件，因为后续调用的`epoll_wait`将不再向程序报告此事件。因此，ET模式在很大程度上降低了同一个epoll事件被重复触发的次数，因此效率比LT模式高。
  
  可以将事件fd上**有数据**认为是高电平，**没有数据**认为是低电平；将事件fd**可写**认为是高电平，**不可写**认为是低电平。那么，**LT模式**的触发条件是状态处于高电平时，而**ET模式**的触发条件是新来一次电信号将当前状态变为高电平。
  
  以socket的读事件举例，对于**LT模式**，只要socket上有未读完的数据，就会一直产生 EPOLLIN 事件；对于**ET模式**，socket 上每新来一次数据就会触发一次，如果上一次触发后，未将 socket 上的数据读完，也不会再触发，除非再新来一次数据。关于socket的写事件，对于**LT模式**，如果 socket 的 TCP 窗口一直不饱和，会一直触发 EPOLLOUT 事件；对于**ET模式**，只会触发一次，除非 TCP 窗口由不饱和变成饱和再一次变成不饱和，才会再次触发 EPOLLOUT 事件。
  
- `EPOLLONESHOT` 事件

  目的：使一个socket连接事件在任意时刻都只能被一个线程处理。当一个线程处理某个socket时，其他线程不能来操作该socket；而当这个socket被某个线程处理完毕后，应当使用`epoll_ctl`重置`EPOLLONESHOT`事件，以确保下一次可读时，`EPOLLIN`事件可被其他工作线程触发。

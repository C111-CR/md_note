## 基础api

- ##### socket

  - 主机字节序和网络字节序

    [大端序和小端序](https://blog.csdn.net/damanchen/article/details/112424874)
  
    ```c++
    // 可以使用以下代码检查机器的字节序
    union{
        short value;			// short: 2个字节
        char union_bytes[2];	// char : 1个字节
    } test;
    test.value = 0x0102;
    if( union_bytes[0] == 1 ) // 低8位存高8位
        cout << "Big Endian." << endl;
    else
        cout << "Little Endian." << endl;
    ```

    现代PC大多是小端字节序，因此小端字节序又被称为主机字节序。当然，还是有使用大端序的主机，因此在使用不同字节序的主机之间传递数据时，就可以在发送端先将数据全部转换成大端字节序再发送出去，而接收端再根据自身字节序来决定需不需要进行转换。

    转换函数：

    ```C++
    #include <netinet/in.h>
    unsigned long int htonl(unsigned long int hostlong);
    unsigned short int htons(unsigned short int hostshort);
    unsigned long int ntohl(unsigned long int netlong);
    unsigned short int ntohs(unsigned short int netshort);
    ```
  
  - socket地址
  
    ```c++
    // 通用socket地址
    #include <bits/socket.h>
    struct sockaddr{
        sa_family_t sa_family;
        char sa_data[14];
    };
    ```
  
    sa_family是地址族，与协议族对应。宏定义PF_*和AF_*具有相同的值，两者通常混用。
  
    |  协议族  |  地址族  |       描述       |
    | :------: | :------: | :--------------: |
    | PF_UNIX  | AF_UNIX  | UNIX本地域协议族 |
    | PF_INET  | AF_INET  |  TCP/IPv4协议族  |
    | PF_INET6 | AF_INET6 |  TCP/IPv6协议族  |

    ```c++
    // 专用socket地址
    // IPv4
    struct sockaddr_in{
        sa_family_t sin_family; // 地址族：AF_UNIX
        u_int16_t sin_port;		// IPv4端口号,用网络字节序表示(二进制数字)
        struct in_addr sin_addr;// IPv4地址,格式同上
    };
    struct in_addr {
        u_int32_t s_addr;		// 无符号32位数表示IPv4地址
    };
    // IPv6
    struct sockaddr_in6{
        // ...
    };
    // UNIX
    struct sockaddr_un{
        // ...
    };
    ```
  
    所有专用socket地址结构体使用时都需要强制类型转换为通用socket地址`sockaddr` 。
  
  - ip地址转换函数
  
    ```c++
    #include <arpa/inet.h>
    // 点分十进制 转换成 网络字节序
    int inet_pton(int af, const char* src, void* dst);
    // 网络字节序 转换成 点分十进制
    const char* inet_ntop(int af, const void* src, char* dst, socklen_t cnt);
    ```
  
    `af` --- 地址族
  
    `socklen_t` --- `INET_ADDRSTRLEN`（IPv4） / `INET6_ADDRSTRLEN`（IPv6）
  
- ##### 线程

  `#include <pthread.h>`

  ```c++
  // 创建线程
  int pthread_create(pthread_t* thread, const pthread_addr_t* attr,
                     void* (*start_routine)(void* ), void* arg
                    );
  ```

  `pthread_t* thread` --- 线程的标识符

  ​		`pthread_t` <==> `unsigned long int` 

  `const pthread_addr_t* attr` --- 用于设置新线程的属性

  ​		`NULL` --- 使用默认的线程属性

  `void* (*start_routine)(void* )` --- 新线程将运行的函数

  `void* arg` --- 运行函数的参数

  ###### 返回值：成功 --- 0, 失败 --- 错误码

  

  ```c++
  // 退出线程
  void pthread_exit(void* retval);
  ```

  `void* retval` --- 用来传递退出信息

  

  ```c++
  // 回收线程
  int pthread_join(pthread_t thread, void** retval);
  ```

  ```c++
  // 终止进程
  int pthread_cancel(pthread_t thread);
  ```

  

  

  

  

  

  

  

  

  
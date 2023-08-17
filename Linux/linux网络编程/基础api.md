## 基础api

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

  

  

  

  

  

  

  

  

  
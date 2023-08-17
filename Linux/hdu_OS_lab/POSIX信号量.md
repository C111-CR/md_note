## POSIX信号量

##### 头文件

```c
#include <semaphore.h>
#include <fcntl.h>
```

### 无名信号量

```c
// create
sem_t *sem_name;
int sem_init(sem_name,int pshared, unsigned int value)
    pshared: >0 //相关进程之间使用
        	 =0 //同个进程之间多个线程使用
    value:  	//初始值
```

```c
// pv operation
int sem_wait(sem_t *sem_name)	
int sem_post(sem_t *sem_name)
return 0:  // 成功
return -1: // 失败
```

```c
int sem_getvalue(sem_t *sem, int *sval);
eg:	int sval;
    if(sem_getvalue(sem,&sval) == -1) 
        perror("sem_getvalue");
int sem_destroy(sem_t *sem);

return 0:   // 成功	
return -1:  // 失败
```

### 有名信号量

```c
//  create
sem_t * sem_open(const char *name,int oflag (,mode_t mode,int value /* when vreate new file */ ) );
```

```c
// close and destroy
int sem_close(sem_t * sem);
int sem_unlink(const char * sem_name);

return 0:  // 成功
return -1: // 失败
```

```c
// pv operation
// 同无名信号量
```






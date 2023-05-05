### fork

###### 1.fork() 为当前进程创建子进程

```c
#include<unistd.h>  //head file
int main(){
    int ret = fork();
    // 对当前进程 ret > 0 (子进程的pid)
    // 对子进程 ret = 0
    // ret < 0 则fork失败
    ...  
    // 子进程会和父进程一样 执行后面的代码
}
```

###### 2.同一个父进程创建多个子进程

```c
int main(){
    int pid_list[4] = {0};
	// pid_list 存储父进程与3个子进程的pid
    
    pid_list[0] = getpid();
    for(int i=1;i<4;i++){
        // 对于子进程 记录pid后就跳出循环
        // 如果不跳出循环 子进程会执行后面循环的代码
        // 会创建子进程的子进程
        if(fork() == 0){
            pid_list[i] = getpid();
            break;
        }
    }
    
    return 0;
}
```


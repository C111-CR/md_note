## exec函数族

作用：用 **<u>指定的另一段程序</u>** 来代替 **<u>当前进程的程序</u>**，即跳转去执行另一个文件

### 1.`execl/execv`

```c
execl(char *path, char* arg1,char* arg2, ... , NULL);
    /* path要用完整的路径 如 /usr/bina.out 
     * 后面是罗列所有的参数 从arg1,arg2,..,argn
     * 注意最后一个参数要是NULL
     */
execv(char* path, char* argv[]);
	/* path仍为完整路径
	 * argv[] 是一个char*类型的数组，存储命令名和所有参数
	 * argv[0] 是命令名
	 * 注意argv[]的最后一个值也是NULL
	 */
```

### 2.`execv/execvp`

```c
execvp(char *file, char *argv[]);
/*
file 为对应命令的名字，如 a.out 或者 ls，不需要输入完整路径
系统通过 PATH 变量，会自动到PATH指定的目录下搜寻可执行文件
如ls - /usr/bin/ls
*/
```

### 3.`exece`

###### 		-e : 环境变量




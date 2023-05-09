# PIPE

```c
int fd[2];			//文件描述符
if(pipe(fd) == 0)	//创建成功
else 				// return -1 创建失败
```

读：fd[0]

写：fd[1]

```c
char buf[BUF_SIZE];
write(fd[1], buf, __/* 写入的字节数 */);
read(fd[0], buf, __/* 读取的字节数 */);
```

#### pipe的缓冲区大小：2^16B = 64KB = 65536B

#### 当一次写入的字节数 <= 4K 时，要实现原子操作


## C语言的内存管理

[cs61c C语言内存管理 youtube-video](https://www.youtube.com/watch?v=JAQg5Ql0QEk&list=PLDoI-XvXO0aqxW0XGUcDl5r0m_eRU30C9&index=2)

首先，一个C程序的内存分为4个部分：栈 - 堆 - 静态内存 - 代码（地址位置从高到低 eg:0xFFFF ~ 0x0000）

#### 1.栈（stack） --- 当前函数

包含：当前函数的位置，函数参数，局部变量

有一个栈指针（SP）,指向栈的最低位置或当前位置（栈从整个程序内存的最高位置开始）

当退出当前函数时，SP会退回到进入这个函数之前的位置

eg: `char str[] = "hi"` --- 存放在栈中

`char *str = "hi"` --- 存放在堆中（相当于`char *str = malloc..`）



#### 2.静态数据（static data）

包含：全局变量，函数内部的静态变量



#### 3.堆（heap）

由于程序在开始执行后，代码内存和静态数据内存的大小是固定的（代码内存基本只读，静态数据内存中的变量值可以被更改，但是sizeof()无法改变）

所以，堆从静态内存的最高位置开始，向栈内存区扩展（堆的大小是动态的）

#### 申请内存空间：

##### a. `void *malloc(size_t size)`  size为要申请的字节数

eg:  申请n个int的内存空间（这片内存不一定连续的），返回起始地址

```c
int *p = (int *)malloc(n * sizeof(int));
```

（malloc返回void*,所以要进行类型转换）

##### b.`void *calloc(size_t num,size_t size)`  

##### num --- 申请的个数   size --- 每个项的大小(字节数)

每个项都被初始化为0

```c
int *p = (int *)calloc(5, sizeof(int));
```

##### c.`void *realloc(void *ptr, size_t size)` 

##### ptr --- 通过m/c/r申请的空间的初始地址   size --- 想要的新空间的字节数



#### 释放内存空间：

`free(p)`  p为指针/地址 --- 要释放空间的起始地址，且这片空间由m/c/realloc 申请而来

（同时，p不能为空或者已经被释放）



#### 4.常见的内存错误

##### a. Segmentation Fault --- 程序试图访问没有分配给他的内存

比如，访问一个为null的指针；访问一个已经被释放的空间的初始地址；

##### b.内存泄漏（memory leaks）

eg:

```c
int *p;

int main(){
    int *p = (int *)malloc(4 * sizeof(int));
    foo();
    free(p); // 无用之举，p已经被覆盖，此时无法再释放那4个int的空间
}

void foo(){
    p = (int *)malloc(8 * sizeof(int));
	free(p);
}
```

在函数foo中 重新申请了一片8个int的空间覆盖了p，所以之前主函数中申请的4个int的空间便泄漏了，因为无法再去释放它

解决方法是，给这4个int的空间加一个副本，如在申请之后，`int *q = p`,之后即使p被更改了，也能通过q来释放这4个int的空间




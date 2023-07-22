## C语言补充

- 共用体 --- 只保留某一个值，其大小为最大的那个数据类型的长度

  ```c++
  union three{
      int intVal;
      long longVal;
      double doubleVal;
  };
  
  three demo;				//demo的长度应该等于一个double的长度
  demo.intVal = 4;
  demo.doubleVal = 17.1;	//此时intVal的值被丢弃
  ```

  ```c++
  // 匿名共用体
  struct node{
      int value;
      union{
          int x;
          double y;
      };
  };
  
  node data;
  data.x = 10;
  data.y = 3.14;		// x和y都作为结构体中的一个元素
  					// 不过他们共用一个数据空间，即同个地址
  ```

  共用体的主要作用是节省内存




















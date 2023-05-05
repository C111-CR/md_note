## GDB

```bash
#编译时
gcc -g {source_file}
gdb {file}(可执行文件)
```

`q/quit`

`r/run`

`c/continue`

`n/next` 	单步执行

​		or {回车键}

`l/list` 	显示前后共10行的代码

`p/print`   变量或者函数

`file ...` 在gdb中打开文件

`b/break {function(函数名)}/{file(.c):n(行数)}` 断点

`d/delete (n)`  加上n表示只删除第n号断点 只有d/delete表示删除所有断点

`i/info` 	查询

​		`i b` 	 	断点信息

​		`i reg/r`  寄存器信息
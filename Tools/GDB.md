## GDB

```bash
#编译时
gcc -g {source_file}
gdb {file}(可执行文件)
```

`q/quit`

`r/run`

`c/continue`

`n/next` 	单步执行 or {回车键}

`file ...` 在gdb中打开文件



`l/list` 	显示前后共10行的代码（10行是默认值）

`l {func(函数名)}`  显示该函数10行代码

`l {num(特定行)}`    显示当前文件第num行的代码

`l {file}:{num}`    显示file文件第num行的代码

`l -` 显示前面10行的代码(注意中间的空格)

`set listsize {num(行数)}`  设置list时显示的代码行数



`p/print`   变量或者函数

`display  {var(变量名)}`   每次执行一步后自动显示该变量的值

`undisplay {num(编号)}`     取消编号对应变量的自动显示

​     == `delete display {num}`

`undisplay`  取消所有变量的自动显示

​	 == `delete display`

###### 注意 undisplay 和 delete display 会在display表中永久删除该变量

###### 如果只是暂时取消查看某变量，可以使用

`disable display num1 num2 ...`

###### 恢复：

`enable display {num(编号)}` 



##### 查看变量

`info locals`       查看当前函数的局部变量

`info args`           查看当前函数的参数

`info variables` 查看全局和静态变量



`b/break {function(函数名)}/{file(.c):n(行数)}` 断点

`d/delete (n)`  加上n表示只删除第n号断点 只有d/delete表示删除所有断点

`i/info` 	查询

​		`i b` 	 	断点信息

​		`i reg/r`  寄存器信息
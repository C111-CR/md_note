## CALL --- Compiler,Assembler,Linker,Loader

#### C语言 helloworld.c  ---  .s

**Compiler**: 将C程序经过优化变成汇编程序（含有伪指令）



#### 汇编语言 helloworld.s  ---  .o

**Assembler**: 将汇编指令（包括伪指令）替换为机器语言（0/1）

​		a. Assembler使用两次代码传递。第一次查找所有标签的地址；第二次替换所有指令（包含伪指令；指令中的标签等相对地址会被解析）

​		b. 汇编程序输出helloworld.o，即一个对象(.obj)文件。该文件由6个部分组成：标题，文本，数据，重新定位表，符号表，调试信息。

​		c. 6个部分：

​					Header:  Sizes and positions of the other parts

​					Text:		The machine code

​					Data:	   Binary representation of any data in the source file

​					Relocation Table: 标记那些需要由Linker重新处理的代码行（如静态数据，或者跳转到外部链接）

​					Symbol Table: 记录代码中的标签和数据

​					Debugging Information: 调试器的附加信息



#### 机器语言 helloworld.o  ---  a.out

**Linker**： a. 将所有对象(.o)文件和需要的库文件(lib.o)链接起来

​				 b. 重新定位代码中的绝对地址引用（如对其他文件标签的引用）



#### 可执行文件 a.out

**Loader**:  将可执行文件从硬盘中加载到内存，并执行它




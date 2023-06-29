## RISC_V 

###### RISC_V --- Reduced Instruction Set Computer V

[RISC-V 中科大开源文档](http://staff.ustc.edu.cn/~llxx/cod/reference_books/RISC-V-Reader-Chinese-v2p12017.pdf)

[RISC-V 介绍视频](https://www.youtube.com/watch?v=lqh9AcrYzUM&list=PLDoI-XvXO0apCzwwwJGKn2p57tnhtSIIz&index=1)



#### A.寄存器

RISC_V有32个寄存器，从x0到x31

？？？浮点寄存器 f0 - f31

0. ##### x0 --- 0寄存器

   x0寄存器的值永远为0；并且无法被写入。

   

1. #####  a0-a7 --- argument registers

   函数参数放在这8个寄存器中（如果参数数量超过寄存器数量，放置在stack中）

   a0-a1: 这两个寄存器在函数结束时，用于放置返回值

   

2. ##### s0-s11 --- saved registers

   用于保存当前函数的一些变量值

   在调用其他函数前，应该把当前函数的saved registers中的内容保存到stack中

   调用该函数结束后，返回当前函数时，把stack中的值回滚到saved registers

   

3. ##### t0-t6 --- temporaried registers

   作为暂时保存值的寄存器

   

4. ##### 寄存器sp --- stack pointer

   使用栈区内存时，将sp向下移动(栈从最高位开始)即相当于获取新栈区空间；

   当函数返回时，回滚sp，即清除了在函数调用过程中使用的内存

   ```assembly
   # store reg t0 to the stack
   addi sp,sp,-4
   sw t0,0(sp)
   # clean up
   ...
   addi sp,sp,4
   ```

   

5. ##### 寄存器ra --- return address

   保存跳转指令的下一条指令的位置

   ```assembly
   1040 jal ra {label}   # 此时pc为1040,则ra会保存1044
   1044 ...
   ...
   2000 label:
   2004	...
   2008	jr ra		  # 跳转到内存地址1044,执行下一条指令
   ```

   

6. ##### gp --- global pointer

   ##### tp --- thread pointer

   

#### B.Instruction

##### 1.无条件跳转

##### 真实指令

jal  (jump and link)

```assembly
jal dst,label # 链接dst寄存器，dst = pc + 4（即下一条指令）；然后跳转到标签
```

jalr (jump and link register)

```assembly
jalr dst,src,imm # 链接dst寄存器，同上；然后跳转到对应地址。
				 # 对应地址 = src寄存器的值+偏移量imm（imm是立即数(即常数)）
```

##### 伪指令: 

j label --- 跳转到该标签 (只能是标签)

###### 		在riscv中，真实指令为 jal x0 label (理由是，x0不会被改变，相当于只执行了跳转)

jr src  --- 跳转到寄存器的值所对应的地址(一般用于 jr ra,即函数返回)

######  		真实指令：jalr x0,src,0 (理由是，x0不会被改变，所以只执行跳转；又因为偏移量为0，所以相当于只执行了 跳转到寄存器src对应的地址)

ret      --- 返回

###### 		 真实指令：jalr x0,ra,0 (即 jr ra)



##### 2.条件跳转

```assembly
# 只能跳转到标签，不能是寄存器的地址
beq reg1,reg2,label # if reg1 == reg2, jump to label
bne reg1,reg2,label # if reg1 != reg2, jump to label
bgt # >
blt # <
bge # >=
ble # <=
```



#### C.函数调用

a. 参数 --- 将参数放在能在函数中使用的地方

b. 跳转到函数

c. 记录然后移动栈指针，给函数工作空间

d. 执行函数，把返回值放在合理的地方

e. 栈指针回滚，清理掉函数调用过程中使用的内存

f.  从函数返回

##### Caller --- 当前函数（调用其他函数）

###### 	work: 保存reg t和reg a(保存那些在当前函数中用到了的a和t寄存器),reg ra以及当前函数变量的值到stack，更新reg a(作为Callee的参数)，更新ra寄存器的值(以便被调用函数执行结束时返回)；Callee执行结束后，把stack中保存的寄存器的值回滚(restore)

##### Callee --- 被调用的函数

###### 	work: 保存Caller的reg s(在Callee中要用到的reg s才需要保存)到stack；执行Callee；返回前把stack中保存的reg s回滚












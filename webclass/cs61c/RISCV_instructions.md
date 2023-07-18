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

##### 1. R型指令 --- 用于寄存器，如add，sub

$\underbrace{funct7}_{7~bits}~\underbrace{rs2}_{5~bits}~\underbrace{rs1}_{5~bits}~\underbrace{funct3}_{3~bits}~\underbrace{rd}_{5~bits}~\underbrace{opcode}_{7~bits}$

其中，由opcode,funct3,funct7共17位共同决定具体是哪条R型指令；rs1与rs2是两个源寄存器，rd是目标寄存器。

eg: add rd,rs1,rs2



##### 2. I型指令 --- 用于带常数的算术指令和load类型的指令

$\underbrace{immediate}_{12~bits}~\underbrace{rs1}_{5~bits}~\underbrace{funct3}_{3~bits}~\underbrace{rd}_{5~bits}~\underbrace{opcode}_{7~bits}$

其中，opcode和funct3共10位共同决定是哪条I型指令；rd是目标寄存器，rs1是源寄存器；

如果是带常数的算术指令，immediate作为一个常数放在第二个源操作数的位置：

addi s0, t0, 0x1A

如果是load类型，immediate作为偏移量（有符号数；补码）与rs1寄存器中的地址相加，得到目标地址；load指令将目标地址中的值取出放在rd寄存器中：

ld s1, 20(a0)				（需要注意的是，偏移量以字节为单位）



##### 3. S型指令 --- 用于存储数据的store类型指令

$\underbrace{immediate[11:5]}_{7~bits}~\underbrace{rs2}_{5~bits}~\underbrace{rs1}_{5~bits}~\underbrace{funct3}_{3~bits}~\underbrace{immediate[4:0]}_{5~bits}~\underbrace{opcode}_{7~bits}$ 

其中，opcode和funct3共10位共同决定是哪条S型指令；由于S型指令不需要目标寄存器（即不需要往某个寄存器中存放数据），所以将原来rd寄存器位置的5 bits和funct7位置的7 bits共同作为immediate（偏移量）；跟load类型指令相同，rs1作为基址寄存器，与偏移量相加后作为要存放数据的地址；rs2中存放要存储到内存的数据。

如：sd rs2, 20(rs1)



##### 4.U型指令 --- 用于处理大立即数的指令

$\underbrace{immediate[31:12]}_{20~bits}~\underbrace{rd}_{5~bits}~\underbrace{opcode}_{7~bits}$

处理方式是：

lui  --- load upper immediate，即取立即数的高位（高20位）存储到寄存器rd寄存器中

eg: 将32位常数 *0x00 3d 05 00* 存储到寄存器t0中

首先，将该常数的前20位使用lui指令加载到t0：lui t0, *0x003d0*

再将t0与后12位算术相加即可： addi t0, t0, 0x500

###### 要注意的是，第二步相加时，0x500=0b0101 0000 0000为正数，所以直接相加即可；如果一个常数的后12位是以1开始（即在相加指令中会被认为是负数），从而使第二条指令变成相减。如常数0x012a0900 --- 0b0000 0001 0010 1010 0000 + 0b1001 0000 0000，解决方法是在第一步lui时，末位加1：即从lui t0,0x012a0 变成 lui t0, 0x012a1。

###### 证明：根据补码的定义，0x900 = $-2^{11}+2^8$ ；而我们想要的是0x900作为无符号数运算，即0x900 = $2^{11}+2^8$ 。所以要从减去$2^{11}$和加上$2^8$变成加上$2^{11}$和加上$2^8$，所以只需要加上2个$2^{11}$，即加上$2^{12}$，即在lui 高20位时，在末位加1即可。



##### 5.分支指令 --- SB型

$\underbrace{imm[12]+imm[10:5]}_{7~bits}~\underbrace{rs2}_{5~bits}~\underbrace{rs1}_{5~bits}~\underbrace{funct3}_{3~bits}~\underbrace{imm[4:1]+imm[11]}_{5~bits}~\underbrace{opcode}_{7~bits}$ 

其中，opcode和funct3共10位共同决定是哪条SB型指令；rs1和rs2作为两个比较数；12位的immediate作为偏移量，其位数设计十分奇葩的原因是为了保持该指令与其他指令在各个位数的对齐；由于偏移量用于指令跳转，所以这12位偏移量以半字（2Byte）为单位，以字节寻址时偏移量需要乘以2(即可以表示13位字节，且最后1位为0)，并且是作为有符号数。

所以SB型指令的跳转范围为： $\pm2^{12}Byte$ 

```assembly
# 只能跳转到标签，不能是寄存器的地址
beq reg1,reg2,label # if reg1 == reg2, jump to label
bne reg1,reg2,label # if reg1 != reg2, jump to label
# 小于则置位（1），都针对于有符号整数
slt reg1,reg2,reg3  # $reg1 = 1 if $reg2 < $reg3
slti reg1,reg2,imm  # $reg1 = 1 if $reg2 < imm
# 对于无符号数
sltu
sltiu

bgt # >
blt # <
bge # >=
ble # <=
```



##### 6.无条件跳转指令 --- UJ型

$\underbrace{imm[20]+imm[10:1]+imm[11]+imm[19:12]}_{20~bits}~\underbrace{rd}_{5~bits}~\underbrace{opcode}_{7~bits}$

只有一条指令是UJ型：jal。immediate的位数设计得如此奇葩的原因依旧是为了与其他类型指令相对齐；同样的是，由于跳转指令以指令为单位，所以imm的单位是半字（2Byte），所以UJ型指令的跳转范围是： $\pm2^{20}Byte$

##### 真实指令

jal  (jump and link)

```assembly
jal dst,label # 链接dst寄存器，dst = pc + 4（即下一条指令）；然后跳转到标签
```

jalr (jump and link register) 

###### --- jalr指令实际上是I型指令

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



#### C.函数调用

a. 参数 --- 将参数放在能在函数中使用的地方

b. 跳转到函数

c. 记录然后移动栈指针，给函数工作空间

d. 执行函数，把返回值放在合理的地方

e. 栈指针回滚，清理掉函数调用过程中使用的内存

f.  从函数返回

##### Caller --- 当前函数（调用其他函数）

###### 	work: 保存reg t和reg a(保存那些在当前函数中用到了的a和t寄存器),reg ra以及当前函数变量的值到stack/s寄存器，更新reg a(作为Callee的参数)，更新ra寄存器的值(以便被调用函数执行结束时返回)；Callee执行结束后，把stack中保存的寄存器的值回滚(restore)

##### Callee --- 被调用的函数

###### 	work: 保存Caller的reg s(在Callee中要用到的reg s才需要保存)到stack；执行Callee；返回前把stack中保存的reg s回滚












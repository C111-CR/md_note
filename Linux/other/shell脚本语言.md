# shell 脚本语言

### shellcheck命令 可以检查脚本文件的规范

```bash
#!/bin/bash
# 指定运行的shell
#!/bin/env bash
```

#### 变量

```bash
num=10     # ‘=’两边没有空格
unset unm  #清除变量num

echo ${num}    # '{}'界定范围 可不加
echo $(ls -l)  # '()'括号内可执行命令

str="abcdefg"
echo ${str}     #输出字符串
echo ${#str}    #输出字符串长度
echo ${str:1:4} #提取子字符串(从a[1]开始截取4个字符:bcde)
```

```bash
export DAY="xxxx:xx:xx"
# export变量会加入到环境变量
```

```bash
set #显示所有变量
```

```bash
read num
```

```bash
readonly var # 设置var只读，不可被修改
```

### $

```bash
echo ${var}/$var  #输出变量值
echo $(cmd)       #括号中可以是一个命令
				  #输出cmd的输出

$0    #当前脚本名
$1-9  #脚本参数
$@    #所有参数
$#    #参数个数

$?    #前一个命令返回值
$$    #当前脚本进程号
```

```bash
!!   #上一条命令
$_   #上一条命令的最后一个参数
#等价于 ESC+.
```

### test

尽量使用双方括号`[[ ]]`, 而不是单方括号`[ ]`

```bash
( expression )
! expression

expression1 -a expression2 #both 1 and 2 are true
expression1 -o expression2 #either 1 or 2 is true
```

```bash
# String
-n string # length of string is nonzero
string    # 同上
-z string # length of string is zero

string1 = string2
string1 != string2
```

```bash
# Integer
a -eq b   # equal
a -ne b   # not equal
a -ge b   # greater equal
a -gt b   # greater than 
a -le b   # less equal
a -lt b   # less than
```

```bash
# FILE
```

### 条件语句

```bash
if [[ {condition} ]];then
	# 要执行的语句
elif [[ {condition} ]];then
	# 要执行的语句
else
	# 要执行的语句
fi
```

### 算术运算

```bash
# (( )) 双括号中会自动解析变量，使用变量不用加 $
((a=a+3))
((a++))
a=$((a+3))
((a=a+3,b=a--))
# 用于整数
```


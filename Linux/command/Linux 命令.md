## linux 命令

#### 1. nautilus  

###### **nautilus**  **file/dir**  显示对应目录或文件的图形窗口（ubuntu）

#### 2.df  查看磁盘使用情况

###### **df -h**  以人类可读的模式显示

#### 3.tldr 简洁版的man/--help（帮助手册）

```bash
tldr ...(a command)
```

`-u`  更新本地命令库

`-v`  查看版本

#### 4.tee 读入stdin然后写入stdout和file

```bash
# 只写入stdout
echo hello | tee
# 写入stdout和一个文件
echo hello | tee file
```

#### 5.xdg-open 使用合适的软件打开文件

```bash
# html -> brower
xdg-open a.html
# it will open a brower
```

```bash
# .c -> editor
xdg-open b.c
# it will open the text editor
```

#### 6. bc  终端数学计算器

```bash
bc -q
```

#### 7. ldconfig 动态链接库管理命令

###### `-p` 打印所有动态链接库

```bash
ldconfig -p | grep xxx  # 搜索某个动态链接库
```

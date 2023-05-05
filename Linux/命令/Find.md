### find 

```bash
find {Path} {Option1} {Option2} ...
```

##### Option:

`-mtime` 修改时间  `-atime` / `-ctime` 

​     `+n` n天前 `-n` n天以内

`-name '...'`  引号中为要匹配的文件名字格式，如 '*.html'

`-size`  `+nk` 超过nKB `-nk` 小于nKB

`-type`  `d` 目录 `l ` 软链接 `p` 管道文件

`-empty`  查找空文件

`-exec`  后面加其他命令，对查找到的文件进行处理

```bash
# ... -exec ...(cmd)  {} ... \;
# ... -exec ...(cmd)  {} ... +
# {}表示找到的所有文件
# \; 表示逐个处理 
# +  表示一起处理
find . -empty -exec rm -f {} \;
find . -name '*.html' -exec tar -cvf html.tar {} +
```


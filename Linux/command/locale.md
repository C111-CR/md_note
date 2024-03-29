### How to set the System language?

###### 1. What is the locale?

a set of environmental variables that defines the language, country, and character encoding settings (or any other special variant preferences)  for your applications and shell session on a Linux system. 

`locale` 是一个关于你的系统一些本地设置的集合。

在命令行中输入`locale` 可以看到如下信息

```bash
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

查阅https://www.gnu.org/software/gettext/manual/html_node/Locale-Environment-Variables.html，可知他们的优先级关系如下

1. LANGUAGE

2. LC_ALL

3. LC_...(除LC_ALL外的其他变量)

4. LANG

关于`locale 变量`的优先级 可以参考[这个链接](https://github.com/tldr-pages/tldr/blob/main/CLIENT-SPECIFICATION.md#language)


##### `LANG` 与 `LC_ALL`

当没有特别指定LC_xxx的值时，LC_xxx默认值就是LANG

因此，可以通过只修改LANG来修改系统的默认语言

同时，当LC_ALL没有被设置的情况下，

我们可以通过设置LANG与LC_xxx来在系统默认语言之外更改某个特定项的语言

如资料中举了一个例子，

For example, assume you are a Swedish user in Spain, and you want your programs to handle numbers and dates according to Spanish conventions, and only the messages should be in Swedish. 

you could set the `LANG` variable to `es_ES.UTF-8` and the `LC_MESSAGES` variable to `sv_SE.UTF-8`.

##### `LC_ALL` 

类似于LANG，但优先级更高

##### `LANGUAGE`

当`LANG`和`LC_ALL`都已经被设置时

系统会按照`LANGUAGE`设置的语言顺序指定语言

一般格式为 `xx1:xx2:xx3`

例如 `LANG=en_US.UTF-8` `LANGUAGE=zh:it:en`

表示系统的语言优先顺序是 中文 > 意大利语 > 英式英语 > 美式英语

（`it` 默认为 `it_IT`,即意大利地区使用的意大利语. `en` `zh` 同理）

###### 参考于 https://www.gnu.org/software/gettext/manual/html_node/The-LANGUAGE-variable.html



###### 2. How to change or set system locale in Linux

首先，可以输入 `locale -a` 查看系统可用的语言

```bash
C
C.utf8
...
en_HK.utf8
...
en_US.utf8
...
POSIX
zh_CN.utf8
```

可以看到 格式一般为 `ll_CC(.utf-8)`

`ll` 为语言 `CC`为地区

如 en_US.utf-8 就是 美国地区的英语，且用 UTF-8 编码

其中C/C.utf-8 似乎是一种默认格式



如果只是更改系统语言

可以只修改 `LANG` 变量

方法如下

###### 1.修改 /etc/default/locale 配置文件（ubuntu）

`vim /etc/default/locale`  

```bash
#  File generated by update-locale
LANG=en_US.UTF-8
LANGUAGE=
```

修改 `LANG` 为想要的语言，再重启即可

###### 2.使用 update-locale 或 localectl 

```bash
sudo update-locale LANG=zh_CN.UTF-8
# or run
sudo localectl set-locale LANG=zh_CN.UTF-8
```

###### 3.为单个用户设置语言

```bash
cd ~
vim .bashrc
# 在文件最末尾插入
LANG=zh_CN.UTF-8 (任何一种可用的语言)
export LANG
```

**前三种方法都是永久生效（系统重启后仍生效）**

###### 4. 临时改变locale变量

```bash
export LANG=zh_CN.UTF-8
```

重启shell后便会重新回到配置文件中的语言



###### 参考于https://www.tecmint.com/set-system-locales-in-linux/
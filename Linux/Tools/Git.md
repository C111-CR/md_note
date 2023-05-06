## Git

蓝桥云课 [Git 与 GitHub 入门实践](https://www.lanqiao.cn/courses/1035)

Git官网    [Git (git-scm.com)](https://git-scm.com/)



### 创建仓库

##### 创建仓库：在GitHub中创建

##### 克隆到本地：git clone {url}

###### ( 克隆的时候，url可以选择http或者通过密钥克隆。采用http形式则每次push的时候需要输入用户名以及密码（详见下文）；而采用密钥形式在push时会自动校对SSH，即不用每次都输入用户名和密码 )

##### 密钥（SSH）

```bash
# create a SSH key
# create a new dir {.ssh} in /home/user
ssh-keygen -t ed25519 -C "the emali"
# 复制~/.ssh/id_ed25519.pub中的公钥内容
# 到 github 中创建SSH...
```

###### （在使用了SSH密钥之后如果采用http形式clone，在push的时候输入用户名及密码时会报错，解决方法是在github中创建一个token作为密码，详见：[创建个人访问令牌](https://docs.github.com/zh/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)）



### Github 

#### 1. `.gitignore`  忽略文件 

###### （eg: 选择C++作为项目语言，会自动忽略 .o 等文件）

###### 创建时可以选择创建相应的`.gitignore`文件 

###### 也可以在创建仓库后，在 [ gitignore 仓库 ](https://github.com/github/gitignore) 中自行下载

#### 2. fork 会克隆一个副本到自己的账号 

###### 包含之前的所有分支与提交，但没有克隆issue

###### 当原仓库发生版本变化时，不会自动同步；反之亦然

#### 3. issue、合作与组员、pull request

##### PR

###### `Create a merge commit` ：这种方式会在组长仓库的 master 分支上生成一个新的提交，且保留 PR 中的所有提交信息。这是一种常规操作，用得最多。

###### `Squash and merge` ：压缩合并，它会把 PR 中的全部提交压缩成一个。此方法的优点就是让提交列表特别整洁。一个 PR 里有很多提交，每个提交都是很细小的改动，保留这些提交没什么意义，这种情况就使用此方法合并 PR。

###### `Rebase and merge` ：这种方法不会生成新的提交，例如 PR 中有 6 个提交，用此方法合并后，组长仓库也会新增 6 个提交。注意，这些提交的版本号与组员的提交不同，此外完全一样。

##### 4. release

###### 标签是 Git 中的概念，而 releases 则是 Github、码云等源码托管商所提供的更高层的概念。Git 本身是没有 releases 这个概念，只有 tag。两者之间的关系则是，release 基于 tag，为 tag 添加更丰富的信息，一般是编译好的文件。

###### ...



### 常用管理命令

#### Git 本地仓库的三大区域

###### 工作区、暂存区、版本区

#### 配置本地信息

```bash
# create a new file {.gitconfig} in dir '~'
git config --global user.name  {github username} # 用户名
git config --global user.email {github email}	 # 邮箱
git config -l 				# 查看配置信息

# 设置别名
git config --global alias.{别名} {'具体命令'}
git config --global alias.br  'branch -avv'
git config --global alias.st  'status'
git config --global alias.com 'commit -m'
```

#### 工作区 <--> 暂存区

##### 查看仓库状态

```bash
git status
```
##### 查看追踪文件的修改情况

```bash
git diff
git diff --cached  # 查看暂存区的修改情况
```
##### 文件交互

```bash
## 将工作区中的文件加入暂存区
git add {file1} {file2} ...
# 或者 一键加入
git add .

## 将暂存区的修改撤销掉
git reset -- {file1} ...
git reset --  # 撤销全部修改
git rm --cached {file1} ...
```

#### 暂存区 <--> 版本区

```bash
## 从暂存区提交到版本区
git commit
git commit -m {'备注'}      # -m是为此次提交添加备注

## 将版本区的提交撤销 即回退到暂存区
git reset --soft HEAD^ 			# 撤销最近一次提交
git reset --soft HEAD~n			# 撤销最近的n次提交			
# 软退回(--soft)和硬退回(--hard)
git reset --hard {版本号}		  # 版本号由reflog命令获取

## 查看提交历史记录
git log 						# 查看当前分支的提交记录
								# 默认最近的提交在最上面
git log {branch name}		  	# 查看指定分支的提交记录
git log --oneline				# 只显示一行
git log -n						# 查看最近n个提交
git log --author {contributor}  # 查看指定贡献者的提交记录
git log --graph					# 图示法显示提交历史
git log --reverse 				# 倒序，最早的提交在最上面

## 查看本地仓库的版本变化
git reflog
```

#### 分支管理

```bash
## 查看分支信息
git branch -avv 					# 查看所有分支

## 创建新分支
git branch {new branch name}		# 创建新分支
git branch -u/{--set-upstream}		# 为当前分支创建跟踪流

## 切换分支
git checkout {branch name}
git checkout -b {new branch name}	# 创建并切换到新分支

## 删除分支(既要删除远程分支 也要删除本地分支)
# 删除远程分支
git push {主机名} :{远程分支名}    # 原理是将空分支推送到远程分支
git push {主机名} --delete {远程分支名} # 通过--delete参数删除
# 删除本地分支
git branch -D {本地分支名1} ...
```

#### 本地仓库 <--> 远程仓库

```bash
## 推送到远程仓库
git push
git push -f						# 强制推送

## 当不是处于主分支而想要push时
# 如果是第一次push
# 需要添加--set-upstream参数(-u)
# 之后的push 直接git push即可
git push --set-upstream {主机名} {本地分支名}:{远程分支名}
# 主机名一般为origin
# 远程分支名一般可以省略
git push --set-upstream origin br1

## 同步本地仓库信息
# 刷新保存在本地仓库的远程分支信息
git fetch					
# 将远程分支的数据同步到本地仓库
git pull
git rebase origin/master
```

#### 当与别人合作时

```bash
## remote
git remote -v				# 查看本地仓库所关联的远程仓库

## 增加一个关联主机 关联到组长的主仓库
# 主机名只是个变量 值为仓库地址 只要不是origin即可(up,upstream..)
# 主仓库地址 https://...
git remote add {主机名} {url 主仓库的地址} 

## 同步主仓库 将主仓库的数据同步到本地
# 先使用fetch拉取主仓库的全部分支信息到本地仓库
# 再使用rebase将远程主仓库的数据同步到本地
git fetch {对应主机名}
git rebase {对应主机名}/master
# 或者使用pull --rebase
git pull --rebase {对应主机名} master
```

#### 当用本地git目录来创建github仓库时

```bash
## 初始化本地仓库
git init
git add .
git commit -m ''
## 先关联github仓库
git remote add {主机名} {ssh_url 仓库地址}
## 提交
git push -u {主机名} {本地分支名}
```

#### 标签 -- 某个提交版本

##### 标签管理

```bash
## 创建标签
# 如果是给当前分支的最新提交创建标签 可以省略版本号
git tag {tag name} -m {'备注'} {提交的版本号}

## 查看标签
git tag							# 查看全部标签
git show {tag name}				# 查看具体标签详情
# 或者直接查看 .git/refs/tags目录

## 删除标签
git tag -d {tag name}			# 删除本地标签

## 将标签更新到远程仓库
git push origin --tags			# 推送所有标签
git push origin {tag name}		# 推送具体标签
# 删除远程仓库中的标签
git push origin :refs/tags/{tag name}
```

##### 标签与分支的应用

```bash
## 如果需要指定某个提交版本进行新的开发
# 先使用标签名切换到想要的提交版本
git checkout {tag name}
# 再创建新的分支
git checkout -b {new branch name}
```



### 不常用命令

```bash 
# git clone -o {主机名} {url} -> 修改主机名

# git init                   -> 将当前目录变成一个本地仓库

# git commit -a  			 -> 将工作区的修改一并提交
#				 			 -> 但会略过未被跟踪的文件

# git branch -m {原分支名} {新分支名}	  
# 							 -> 更改本地分支名字
```



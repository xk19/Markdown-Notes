## 一、基本理论

### 1、版本控制

- **本地版本控制**

- **集中式版本控制——SVN**

	保存在中央服务器上，用户需要联网同步，完成工作后推送到中央服务器，不方便。

- **分布式版本控制——Git**

	没有中央服务器，每个用户都拥有完整的版本库，完成修改后只需要把各自的修改推送给对方即可，方便快捷，但对公司而言存在安全隐患，容易带库跑路。

### 2、启动方式

> **Git Bash**：Unix与Linux风格的命令行，使用最多，推荐最多
>
> **Git CMD**：Windows风格的命令行
>
> **Git GUI**：图形界面的Git，不建议使用

### 3、工作区域

> **Workspace**：工作区，平时存放项目代码的地方
>
> **Index / Stage**：暂存区，用于临时存放你的改动，事实上它只是一个文件，保存即将提交到文件列表信息
>
> **Repository**：本地仓库，就是安全存放数据的位置，这里面有你提交到所有版本的数据。其中HEAD指向最新放入仓库的版本
>
> **Remote**：远程仓库，托管代码的服务器

### 4、常用操作与状态

![image-20231206170737290](C:/Users/admin/AppData/Roaming/Typora/typora-user-images/image-20231206170737290.png)

> - **常用操作**：git add、git commit、git push、git pull、git checkout、git clone
> - **四种状态**：Untracked、Unmodify、Modified、Staged

### 5、设置远程

> 文件目录：*C:\Users\Administrator\.ssh*
>
> 生成公钥：ssh-keygen
>
> 生成加密公钥：ssh-keygen -t rsa -C “email”
>
> 查看生成的公钥文件：cat ~/.ssh/id_rsa.pub

## 二、常用操作

### 1、配置指令

> - **查看配置**：git config -l
>
> - **查看系统配置**：git config --system --list
>
> - **查看当前用户配置**：git config --global  --list
>
> - **配置用户名**：git config --global user.name "name"
>
> - **配置密码**：git config --global user.email  “email”
>
> - **配置代理端口**：git config --global http.proxy http://127.0.0.1:10809    git config --global https.proxy http://127.0.0.1:10809
>
> - **删除代理端口**：git config --global --unset http.proxy                                git config --global --unset https.proxy
>

> Git\etc\gitconfig ：Git 安装目录下的 gitconfig   --system 系统级
>
> C:\Users\admin\ .gitconfig ：只适用于当前登录用户的配置  --global 全局

> 配置环境变量的作用：为了全局使用

### 2、操作命令

> - **本地仓库搭建**：git init
> - **查看所有文件状态**：git status
> - **查看指定文件状态**：git status [filename]
> - **添加所有文件到暂存区**：git add .
> - **添加指定文件到暂存区**：git add [file1] [file2]
> - **提交暂存区到工作区**：git commit -m "消息内容"
> - **删除工作区文件**：git rm [file1] [file2] ...
> - **上传远程代码并合并**：git push
> - **下载远程代码并合并**：git pull
> - **克隆远程仓库**：git clone [url]
> - **重命名当前分支名称**：git branch -M main
> - **增加一个新的远程仓库并命名**：git remote add [shortname] [url]
> - **将本地的主分支推送到远程**：git push -u origin main

> - **列出所有本地分支**：git branch
> - **列出所有远程分支**：git branch -r
> - **列出所有分支**：git branch -a 
> - **新建一个分支，但依然停留在当前分支**：git branch [branch-name]
> - **新建一个分支，并切换到该分支**：git chechout -b [branch-name]
> - **切换分支，并更新工作区**：git checkout [branch-name]
> - **合并指定分支到当前分支**：git merge [branch]
> - **删除分支**：git branch -d [branch-name]
> - **删除远程分支**：git push origin --delete [branch-name]     git branch -dr [remote/branch]
> - **取回远程仓库的变化，并与本地分支合并**：git pull [remote] [branch]

### 3、常用的Linux命令

>- cd : 改变目录
>- cd . . 回退到上一个目录，直接cd进入默认目录
>- pwd  显示当前所在的目录路径
>- ls(ll) 列出当前目录中的所有文件，加ll(两个ll)列出的内容更详细
>- touch 新建文件
>- rm 删除一个文件, rm index.js 就会把index.js文件删除
>- mkdir:  新建一个目录,就是新建一个文件夹
>- rm -r :  删除一个文件夹, rm -r src 删除src目录
>- mv 移动文件, mv index.html src index.html 是我们要移动的文件, src 是目标文件夹,当然, 这样写,必须保证文件和目标文件夹在同一目录下
>- reset 重新初始化终端/清屏
>- clear 清屏
>- history 查看命令历史
>- help 帮助
>- exit 退出
>- \# 表示注释

## 三、忽略文件

在主目录下建立".gitignore"文件，此文件有如下规则：

1. 忽略文件中的空行或以井号（#）开始的行将会被忽略。
2. 可以使用Linux通配符。例如：星号（*）代表任意多个字符，问号（？）代表一个字符，方括号（[abc]）代表可选字符范围，大括号（{string1,string2,...}）代表可选的字符串等。
3. 如果名称的最前面有一个感叹号（!），表示例外规则，将不被忽略。
4. 如果名称的最前面是一个路径分隔符（/），表示要忽略的文件在此目录下，而子目录中的文件不忽略。
5. 如果名称的最后面是一个路径分隔符（/），表示要忽略的是此目录下该名称的子目录，而非文件（默认文件或目录都忽略）。

> ```
> #为注释
> *.txt        #忽略所有.txt结尾的文件,这样的话上传就不会被选中
> !lib.txt     #但lib.txt除外
> /temp        #仅忽略项目根目录下的TODO文件,不包括其它目录temp
> build/       #忽略build/目录下的所有文件
> doc/*.txt    #会忽略doc/notes.txt 但不包doc/server/arch.txt

## 四、参考

[Git大全](https://gitee.com/all-about-git)

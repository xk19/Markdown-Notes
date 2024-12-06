### 1、常用Linux命令

> - 罗列文件：ls -[o]\[f]
> - 罗列文件及属性：ls -l
> - 显示所有文件，包含隐藏文件：ls -a
> - 显示当前文件夹：pwd
> - 进入用户主目录 ：cd
> - 返回进入之前的目录：cd -
> - 返回到上级目录：cd ..

> - 创建文件：touch [filename]
> - 重定向的方式创建文件：>[filename]
> - 创建文件夹：mkdir [filename]
> - 递归创建文件夹：mkdir -p [filename1/filename1]
> - 创建隐藏文件：touch .[filename]
> - 删除空目录：rmdir [filename]
> - 删除文件：rm [-r(递归)、-f(强制)] [filename]
> - 拷贝文件：cp [-r(递归)、-f(强制)] [filename] [directory]
> - 移动文件或者目录：mv [filename] [directory]
> - 文件重命名：mv [filename] [filename]
> - 搜索文件：find [directory] [filename]
> - 显示文本内信息：cat/tac/more/less [filename]
> - 显示文件前/后几行：head/tail -[k] [filename]
> - 显示1000-1010行：head -1010 [filename] | tail -10
>
> - 显示文件具体信息：stat [filename]
> - 显示当前文件树状结构：tree .
> - 输出字符或者字符串：echo

> - 文本内容行匹配：grep [-n(带行输出) -i(忽略大小写) -v(方向选择)] ’[keyword]‘ [filename]
> - 文本内容排序：sort [filename]
> - 文本内容去重：uniq [filename]
> - 文件压缩：zip  -r [filename.zip] [directory]
>
> ​                           tar -czf [filename.tgz] [directory]
>
> - 文件解压：unzip -r [filename.zip]
>
> ​                           tar -xzf [filename.tgz]
>
> - 文件解压到指定路径：unzip [filename1.zip] -d [directory]

> - 超级用户：sudo
> - 新建用户：adduser [username]
> - 设置用户密码：passwd [username]
> - 删除用户：userdel -r [username]

> - 查询Linux帮助手册：man [command]
> - 查询相关命令：部分字符+TAB
> - 终止进程：ctrl+c
> - 搜索命令：which [command]
> - 对命令重命名：alisas  [name] = ‘[command]’

### 2、XShell命令

> - 登入：ssh [username@IP]      (root@43.143.222.136)
> - 退出登入：logout
> - 复制：ctrl insert
> - 粘贴：shift insert

### 3、Vim编辑器

>- 进入Vim工作模式：vim filename
>- 退出Vim工作模式：命令模式下输入wq
>- 切换到命令模式：Esc
>- 切换到输入模式：输入i/a/o
>- 切换到底部命令模式：输入":"
>- 停止界面刷新：ctrl  s
>- 恢复界面刷新：ctrl q

### 4、GCC/G++/GDB

> - 安装C++编译器：sudo yum install gcc-c++
>
>   ​                              sudo apt-get install g++
>
> - 查询g++版本：g++  --versiom
>
> - 生成可执行文件test.out：g++ [test.cpp] -o [test]
>
> - 运行可执行文件test.out：./test.out

### 常用包安装

> - 树状图目录显示包tree：sudo yum install tree
> - 文件压缩/解压包zip：sudo yum install zip unzip


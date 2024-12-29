## 一、常用Linux命令

### 1、文件操作

> - 罗列文件：ls -[command]
> - 罗列文件及属性：ls -l / ll
> - 显示所有文件，包含隐藏文件：ls -a
> - 显示当前文件夹：pwd
> - 进入用户主目录 ：cd
> - 返回进入之前的目录：cd -
> - 返回到上级目录：cd ..
> - 显示某个文件夹：ls [directory] -d

> - 创建文件：touch [filename]
> - 重定向的方式创建文件：>[filename]
> - 创建文件夹：mkdir [filename]
> - 递归创建文件夹：mkdir -p [filename1/filename1]
> - 创建隐藏文件：touch .[filename]
> - 删除空目录：rmdir [filename]
> - 删除文件：rm [-r(递归)、-f(强制)] [filename]
> - 删除当前所有文件：rm * -rf
> - 拷贝文件：cp [-r(递归)、-f(强制)] [filename] [directory]
> - 移动文件或者目录：mv [filename] [directory]
> - 文件重命名：mv [filename] [filename]
> - 搜索文件：find [directory] [filename]
> - 显示文本内信息：cat/tac/more/less [filename]
> - 显示文件前/后几行：head/tail -[k] [filename]
> - 显示1000-1010行：head -1010 [filename] | tail -10
> - 显示文件具体信息：stat [filename]
> - 显示当前文件树状结构：tree .
> - 输出字符或者字符串：echo
> - 输入重定向：<
> - 输出重定向：>
> - 输出追加重定向：>

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
> - 文件解压到指定路径：unzip [filename.zip] -d [directory]
>
> ​                                              tar zxvf [filename1.zip] -C [directory]

> - 查看文件的状态信息：stat [filename]
> - 更新文件时间：touchu [filename]
> - 查看文件类型及编码格式：file [filename]
> - 列出可执行文件或者共享库文件所的依赖：ldd [filename]

> - 以下为系统接口函数（头文件查询手册man 2）
>
> - 文件打开：open（[*pathname], [flags], [mode]）
>
> - 文件关闭：close（[fd]）
>
> - 文件写入：write（[fd], [*buf], count）
>
> - 文件读取：read（[fd], [*buf], count）
>
> - 更改进程  ans（[mask]）
>
>   ```
>   系统调用接口：open / close / write / read / lseek
>   库函数接口：fopen / fclose / fwrite / fread / fseek
>   ```
>
> - 文件重定向：dup2（[oldfd], [newfd]）

### 2、用户及权限

> - 新建用户：adduser [username]
> - 设置用户密码：passwd [username]
> - 删除用户：userdel -r [username]
> - 查询登入主机：who
> - 查询当前用户：whoami
> - 退出当前用户：ctrl+d
> - 普通用户切换成root用户：su / su -    （只有su -会重新登入，并切换环境变量和配置文件）
> - root用户切换成普通用户：su [username]
> - 普通用户用root身份执行命令：sudo [command]
> - 给用户添加sudo权限：
>
>   ```
>   1、su切换成root用户	2、ls /etc/sudoers	3、vim /etc/sudoers
>   4、第100行前后对应部分，加入username ALL=(ALL) ALL	5、强制写入和退出
>   ```

> - 修改own的文件权限：chmod u[+/-]\[r/w/x] [filename]
> - 修改group的文件权限：chmod g[+/-]\[r/w/x] [filename]
> - 修改other的文件权限：chmod o[+/-]\[r/w/x] [filename]
> - 同步修改所有人的文件权限：chmod  a[+/-]\[r/w/x] [filename]
> - 以八进制修改文件权限：chmod  [777] [filename]
> - 修改文件的own：sudo chown [own] [filename]
> - 修改文件的group：sudo chgrp [own] [filename]
> - 给目录添加粘滞位：chmod  +t [filename]

### 3、进程

> - 状态说明：R（running 运行）   R+（前台运行）S（sleeping 睡眠/阻塞）
>
>   ​		   T（stopped 停止） t（tracing stop 停止） Z（Zombies 僵尸）
>
> - 查看进程：ps  -la
>
> - 查看特定进程：ps ajx | grep '[processname]'         （还可以通过ls /proc查看）
>
> - 查找并查看进程对应信息：ps ajx | head -1 && ps ajx | grep '[processname]'
>
> - 进程监控脚本：while :; do ps ajx | head -1 && ps ajx | grep '[processname]' | grep -v grep; sleep 1; done
>
> - 获取进程信息：getpid()      包含头文件<sys/types.h>
>
> - 获取父进程信息：getppid()      包含头文件<sys/types.h>
>
> - 创建子进程：fork()    包含头文件<unistd.h>
>
> - 查看kill选项：kill -l 
>
> - 杀死进程：kill -9 [PID]
>
> - 暂停进程：kill -19 [PID]
>
> - 继续进程：kill -18 [PID]
>
> - 返回最近一个进程的退出码：echo $?
>
> - 退出码函数：strerror(i)      需要包含头文件<string.h>
>
> - 进程退出：
>
>   ```
>   #include<stdlib.h>
>   exit(n)     库函数，主动刷新缓冲区
>   _exit(n)    系统调用，不会刷新缓冲区
>   return [n]  n为退出码
>   WIFEXITED(status)  是否正常退出
>   WEXITSTATUS（status） 判断子进程运行结果是否OK
>   ```
>
> - 进程等待：
>
>   ```
>   wait(&status)     包含头文件<sys/types.h>和<sys/wait.h>
>   wait(pid, &status, option)
>   ```
>
> - 进程替换函数：
>
>   ```
>   #include<unistd.h>
>   int execl(const char *path, const char *arg, ...);
>   int execlp(const char *file, const char *arg, ...);
>   int execle(const char *path, const char *arg, ...,char *const envp[]);
>   int execv(const char *path, char *const argv[]);
>   int execvp(const char *file, char *const argv[]);
>   ```
>
> - 修改进程的当前目录：chdir([directory])          包含头文件<uinistd.h>

> - 查看环境变量：echo $PATH
>
> - 将路径加入到环境变量中：export PATH=$PATH:[directory]
>
> - 导入一个局部环境变量：export [valname] = [n]
>
> - 查看全局环境变量：env | grep [valname]
>
> - 查看本地定义的shell变量或者全局环境变量：set | grep [valname]
>
> - 查看某个环境变量：echo &[valname]
>
> - 清除环境变量：unset [valname]
>
> - 代码中获取环境变量：getenv()      包含头文件<stdio.h>
>
>   ​       				environ      包含头文件<unistd.h>

### 4、网络

> - 查询公网IP地址：curl ifconfig.me

### 5、其他命令

> - 命令自动补齐：部分字符+TAB
> - 查询Linux帮助手册：man [command]
> - 终止前台的异常进程：ctrl+c
> - 搜索历史命令：ctrl+r
> - 匹符配任意字符的通配符：*
> - 查新系统信息/架构：uname -a/r
> - 查看CPU情况：lscpu
> - 查看内存信息：lsmem
> - 查看磁盘使用情况：df -h
> - 搜索命令：which [command]
> - 对命令重命名：alisas  [name] = ‘[command]’

### 6、函数

> - 延时函数：sleep(n)      包含头文件<unistd.h>
> - 强制程序刷新输出：fflsh(stdout)
> - 获取输入字符流：fgets(*[s], [size], *[stream])         包含头文件<stdio.h>
> - 字符串切割函数：strtok([str], " ")              包含头文件<string.h>
> - 检查空格：isspace([ch])      包含头文件<ctype.h>
> - 字符串转整形：atoi([str])   /   stoi([str])
> - 整形转字符串：itoa([str]) 

## 二、常用软件包及命令

### 1、XShell命令

> - 登入：ssh [username@IP]      (username@43.143.222.136    腾讯云)
> - 退出登入：logout
> - 复制：ctrl insert
> - 粘贴：shift insert

### 2、Vim编辑器

>- 打开文件并进入命令模式：vim [filename]
>- 切换到命令模式：Esc
>- 切换到插入模式：输入i/a/o
>- 命令模式下切换到底行模式：shift + ’:‘
>- 退出vim：底行模式下输入wq
>- 停止界面刷新：ctrl  s
>- 恢复界面刷新：ctrl q

>- （以下操作均为命令模式下）
>- 光标定位行右：shift 4
>- 光标定位行左：shift 6
>- 光标定位底部：shift g
>- 光标定位顶部：gg
>-  跳转光标到第[n]行：[n] shift g
>- 复制光标所在行以下[n]行：[n]yy
>- 粘贴n遍复制的内容：[n]p
>- 撤销操作：u
>- 取消撤销：ctrl r
>- 删除光标所在行以下[n]：[n]dd
>- 剪切光标所在行以下[n]并粘贴：[n]dd + p
>- 大小写转换：shift ~
>- 批量化替换：shift r
>- 批量化注释：ctri v + j/k + I + // + ESC
>- 单字符替换：r
>- 行内向后/向前删除（可用nx）：x / shift x

>- （以下操作均为底行模式）
>- 设置/取消行号：set nu / nonu
>- 保存与退出：w（保存） q（退出） ！（强制）
>- 分屏：vs
>- 光标跨屏：ctrl ww
>- 底行执行Linux命令：！ [command]
>- 底行执行Linux命令后退出：q
>- 用[a]替换文本中的[b]：%s/[a]/[b]/g

>- 针对单个用户配置vim：
>
>  ```
>  1、cd ~		2、touch .vimrc（该文件下增加配置设置）
>  3、如果是Centos7，针对单个用户建议配置一键配置vim（不推荐root下）：
>  curl -sLf https://gitee.com/HGtz2222/VimForCpp/raw/master/install.sh -o ./install.sh && bash ./install.sh
>  ```
>

### 3、GCC/G++/GDB

> - 安装C++编译器：sudo yum install gcc-c++
>
>   ​                              sudo apt-get install g++
>
> - 查询g++版本：g++  --versiom
>
> - 生成预处理文件：g++ -E [test.cpp] -o [test.i]
>
> - 生成汇编语言：g++ -S [test.i] -o [test.s]
>
> - 生成二进制目标文件：g++ -c [test.s] -o [test.o]
>
> - 链接生成可执行文件：g++ [test.o]
>
> - 生成可执行文件：g++ [test.cpp] -o [test]
>
> - 运行可执行文件test.out：./test.out
>
> - 静态链接并生成可执行该文件：gcc [test.cpp] -o [test] -static

> - 编译成release版本：gcc [test.cpp] -o [test] -g
> - 调试文件：gdb [namefile]
> - （以下是在调试模式下）
> - 显示代码：l      （后面加n从n行开始显示，回车继续显示）
> - 在第[n]行打断点：b [n]
> - 查看断点：info b
> - 去掉第[n]个断点：d [n]
> - 运行程序：r（run）
> - 单步执行：n（next）
> - 进入函数调用：s（step）
> - 运行至下一个断点处：c（continue）
> - 查看堆栈：bt
> - 直接运行到将当前函数跑完：finish
> - 查看变量：p [var]
> - 修改变量的值：set [var]
> - 常显示变量：display [var]
> - 常显示地址：display &[var]
> - 取消常显示编号为[n]的变量或地址：display [n]
> - 查看当前栈帧局部变量的值：info locals
> - 直接跳转到第[n]行：until [n]
> - 退出：quit

### 4、安装软件yum

> - 安装软件：[sudo(提权)] yum install [-y(全部默认安装)] [package]
> - 搜索可安装的相关文件：yum list | grep [keyword]
> - 卸载软件：yum remove [-y(全部默认卸载)] [package]
> - 查看yum源（Base为yum默认采用）：ls /etc/yum.repos.d/          vim /etc/yum.repos.d/CentOs-Base.repo
> - 安装非官方的软件集合列表：yum install -y epel-release

### 5、Make/Makefile

> make命令：运行Makefile（m大小写均可），默认从上到下，只形成一个可执行文件
>
> Makefile组成：
>
> ```
> # 定义变量
> CC = gcc
> CFLAGS = -Wall
> 
> # 规则：从依赖文件生成目标文件（可用$@代表：左侧的文件，$^代表：右侧的文件）
> [object]: [source .c]
> 	gcc -o [object] [source .c]
> 
> # 伪目标(总是被执行)：清理构建文件
> .PHONY: clean
> clean:
>   rm -f *.o program
> ```
>
> 注释：#

### 其他常用包安装

> - 树状图目录显示包tree：sudo yum install tree
> - 文件压缩/解压包zip：sudo yum install zip unzip
> - 记事本nano：sudo yum install -y nano
> - 互传文件lrzsz：sudo yum install -y lrzxz
> - 对centos安装c语言静态链接库：sudo yum install -y glibc-static
> - 对centos安装c++静态链接库：sudo yum install -y libstdc++-static
> - Linux帮助手册：sudo yum install -y man-pages


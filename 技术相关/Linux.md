### 1、常用Linux命令

> - 罗列文件：ls -[o]\[f]
> - 显示当前文件夹：pwd
> - 创建文件：touch filename
> - 创建文件夹：mkdir filename
> - 超级用户：sudo
> - 复制文件：cp
> - 通配符：hw_*.service
> - 查询python版本：python3 --version /(usr/bin) 

![image-20241206105959376](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20241206105959376.png)

### 2、Shell命令

> - 登入：ssh [ip]          (43.143.222.136)
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

> - 安装C++编译器：sudo apt-get install g++
>
>   ​                              sudo yum install gcc-c++
>
> - 查询g++版本：g++  --versiom
>
> - 生成可执行文件test.out：g++ [test.cpp] -o [test]
>
> - 运行可执行文件test.out：./test.out
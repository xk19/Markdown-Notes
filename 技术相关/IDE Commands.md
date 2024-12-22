## 一、Visual Studio

> - 注释选定内容：ctrl + k + c
>- 取消注释选定内容：ctrl + k + u
> - 开始执行（不调试）：Ctrl + F5
> - 启动调试：F5
>- 单步逐过程执行：F10
> - 逐语句调试：F11

## 二、PyCharm

> - ctrl alt l：自动缩进
>
> - ctrl /：添加/消除注释
>
> - tab / shift tab：代码 右移/左移
>
> - ctrl shift -：折叠所有代码
>
> - ctrl shift +：展开所有代码
>
> - ctrl x：删除整行代码
>
> - ctrl b：跳转到对象的声明处
>
> - alt shift e：选定的脚本代码在控制台运行
>
> - ctrl shift A：万能命令行
>
> - shift shift：查看资源文件

## 三、Anaconda

### 1、环境

> - 创建环境：conda create -n env_name python=3.x
>
> - 查看anaconda环境信息：conda info --env
>
> - 激活环境：cond -a activate env_name 
>
> - 解除激活环境：conda deactivate
>
> - 删除环境：conda remove -n env_name --all
>
> - 删除conda里面多余的包：conda clean -a-a
>
> - 查询环境是否兼容：pip check
>
> - 查询显卡的cuda版本：nvidia-smi
>
> - 查询安装的cuda版本：print(torch.version.cuda)；print(torch.__version__)
>
> - 查询是否可以使用GPU：python；import torch；torch.cuda.is_available()
>
> - 查询cuda版本：nvidia-smi
>
> - 进入Jupyter Notebook：Jupyter-notebook 
>
> - 其他文件夹打开Jupyter：Jupyter-notebook path_name
>
> - 退出Jupyter Notebook：ctrl+c

### 2、库文件/包

> - 列出当前环境的包：conda / pip list
>
> - 列出其他环境的包：conda list -n env_name 
>
> - 安装新的包：conda / pip install package_name
>
> - 卸载某个包：conda / pip uninstall package_nam
>
> - 安装特定版本的包：conda install package_name==version
>
> - 查询可安装的版本：pip install package_name==
>
> - 搜索某个包：conda search package_name
>
> - pip查看当前版本包：pip show package_nameinstall
>
> - 查看pip下载的镜像源：pip config list
>
> - 配置pip的镜像源：pip config set global.index-url 
>
> - conda查看配置：conda config --show
>
> - conda添加下载的镜像源：conda config --add channels 
>
> - conda虚拟环境的备份：conda create -n conda-env2 --clone conda-env1（conda-env2是新创建的Conda环境、conda-env1是被复制的Conda环境；复制完成后，两个环境的Python配置是完全相同的，可以直接使用）

### 3、Jupyter Notebook

> - ctrl enter : 运行本单元
>
> - shift enter：运行本单元，选中下个单元
>
> - alt enter : 运行本单元，在其下插入新单元
>
> - ctrl d/c/x/v：删除/复制/剪切/粘贴
>
> - ctrl ] ：缩进
>
> - ctrl [ ：解除缩进
>
> - chift tab : 提示
>
> - 切换模式：Esc/Enter
>
> - 调出快捷键：命令模式下打H
>
> - 删除Cell：dd
>
> - 上/下方加入Cell：命令模式下a/b

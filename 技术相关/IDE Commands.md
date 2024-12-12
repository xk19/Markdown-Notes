## 一、Visual Studio

> 

## 二、Anaconda

### 1、环境

> 创建环境：conda create -n env_name python=3.x
>
> 查看anaconda环境信息：conda info --env
>
> 激活环境：cond -a activate env_name 
>
> 解除激活环境：conda deactivate
>
> 删除环境：conda remove -n env_name --all
>
> 删除conda里面多余的包：conda clean -a-a
>
> 查询环境是否兼容：pip check
>
> 查询显卡的cuda版本：nvidia-smi
>
> 查询安装的cuda版本：print(torch.version.cuda)；print(torch.__version__)
>
> 查询是否可以使用GPU：python；import torch；torch.cuda.is_available()
>
> 查询cuda版本：nvidia-smi
>
> 进入Jupyter Notebook：Jupyter-notebook 
>
> 其他文件夹打开Jupyter：Jupyter-notebook path_name
>
> 退出Jupyter Notebook：ctrl+c

### 2、库文件/包

> 列出当前环境的包：conda / pip list
>
> 列出其他环境的包：conda list -n env_name 
>
> 安装新的包：conda / pip install package_name
>
> 卸载某个包：conda / pip uninstall package_nam
>
> 安装特定版本的包：conda install package_name==version
>
> 查询可安装的版本：pip install package_name==
>
> 搜索某个包：conda search package_name
>
> pip查看当前版本包：pip show package_nameinstall
>
> 查看pip下载的镜像源：pip config list
>
> 配置pip的镜像源：pip config set global.index-url 
>
> conda查看配置：conda config --show
>
> conda添加下载的镜像源：conda config --add channels 
>
> conda虚拟环境的备份：conda create -n conda-env2 --clone conda-env1（conda-env2是新创建的Conda环境、conda-env1是被复制的Conda环境；复制完成后，两个环境的Python配置是完全相同的，可以直接使用）

## 三、PyCharm

> 




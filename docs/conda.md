## conda

### 安装

如果你能够拥有sudo权限的话，那么安装conda是一件非常简单的事情的。但是在一个多人同时使用的服务器上，往往我们面临的情况是，我们仅仅只有普通用户的权限。因此，这时候，我们只能够采用安装包的方式来完成软件的安装。

完整的anaconda相对来说比较庞大，在硬盘资源和网络带宽资源较为有限的情况下，这种安装会消耗大量的时间。因此，这里我们采用最小安装包安装conda，在后续有需要的时候再加入其他组件。

在使用安装包来安装miniconda3时，你首先需要根据自己的操作系统和服务器架构确定合适的安装包。安装包的下载页面可通过该 [链接](https://repo.anaconda.com/miniconda/) 进入。之后在其中选择合适版本的安装包下载。

这里，我们提供一个在Linux操作系统、x86架构下的安装命令。

```shell
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
~/miniconda3/bin/conda init bash
```

上述命令执行完成后，我们就完成了miniconda的配置了。重新打开shell后，你就可以看到conda的显示。重新启动后，默认会进入base的环境。

#### 错误排查

有时候，服务器上存在管理员权限用户安装conda时可能会把我们的环境变量所污染，导致初始化bash的命令`~/miniconda3/bin/conda init bash`因为选择了错误的配置文件，导致conda的初始化命令没有正确执行。为此，我们可以通过如下命令来修正默认conda读取配置文件的路径：

```shell
export CONDA_EXE=/home/qrh/miniconda3/.condarc
export CONDA_PREFIX=/home/qrh/miniconda3/.condarc
export CONDA_PYTHON_EXE=/home/qrh/miniconda3/.condarc
```

### 配置

有时候，我们可能不希望一进入shell就看到conda的base环境，我们可以通过配置来取消默认启动base环境。取消后，你可以通过`conda activate base`来再进入base环境。

```shell
conda config --set auto_activate_base false
```

conda中默认包的下载路径会走国外的下载路径，这在国内下载会相对较慢。为此，我们可以将conda的默认源修改为国内的一些镜像站的源，以加快日常使用过程中包的下载速度。

conda的配置文件为用户根目录下的`.condarc`文件，我们可以直接通过该文件来对conda进行配置。这里，我们给出采用浙江大学镜像站的配置方案：

```yaml
channels:
  - defaults
show_channel_urls: true
default_channels:
  - https://mirrors.zju.edu.cn/anaconda/pkgs/main
  - https://mirrors.zju.edu.cn/anaconda/pkgs/r
  - https://mirrors.zju.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.zju.edu.cn/anaconda/cloud
  msys2: https://mirrors.zju.edu.cn/anaconda/cloud
  bioconda: https://mirrors.zju.edu.cn/anaconda/cloud
  menpo: https://mirrors.zju.edu.cn/anaconda/cloud
  pytorch: https://mirrors.zju.edu.cn/anaconda/cloud
  pytorch-lts: https://mirrors.zju.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.zju.edu.cn/anaconda/cloud
```

如上配置后即可添加 Anaconda Python 仓库。

运行 `conda clean -i` 清除索引缓存，保证用的是镜像站提供的索引。

### 环境分享

有时候，我们需要把自己的conda环境分享给其他人，整个文件复制肯定是十分麻烦的。所幸，在conda中我们可以通过一个yml人家来实现环境的分享，帮助对方快速建立一个新的环境。

生成环境描述文件命令：

```shell
conda env export > environment.yml
```

根据环境文件创建环境命令：

```shell
conda env create -f environment.yml
```

### 环境卸载

如果有一天你不想使用某个环境了，你可以使用以下命令删除该虚拟环境。

首先，我们需要退出虚拟环境。

```shell
conda deactivate
```

然后，将虚拟环境以及其中所有的包删除即可。

```shell
conda remove -n env --all
```

### tensorflow 环境安装示例

本示例中，我们所使用的Python版本为3.6，TensorFlow版本为tensorflow-gpu 1.11. cuDNN版本为7，CUDA版本为9.

为了创建相应的环境，你可以按照如下步骤进行操作。

首先，我们需要创建运行tensorflow的conda环境。

```shell
conda create -n tensorflow python=3.6
```

接着，我们需要进入虚拟环境之中。

```shell
conda activate tensorflow
```

进入虚拟环境之后，我们就可以通过pip来按照TensorFlow了。

如果你只是打算进行实验，采用CPU版本运行的话，你可以直接使用下述命令进行安装，之后便可跳过后面的内容了。如果你打算采用GPU版本运行的话，请不要执行下述命令。

```shell
pip install tensorflow==1.11
```

当然，只有CPU的运行效率是极低的，我们肯定还是要借助GPU的力量才能最大程度发挥计算机的性能。

所以，让我们一起来试着安装GPU版本的TensorFlow吧。

如果你已经执行了上面那条安装CPU版本TensorFlow的命令的话，你可能需要先把CPU版本的TensforFlow卸载。

```shell
pip uninstall tensorflow
```

那么，GPU版本TensorFlow的安装正式开始。

我们首先需要看看在目前的Anaconda环境中有哪些可用的`cudatoolkit`和`cudnn`。你可以分别采用下面的命令来查询。

检查当前的cuda版本号:

```shell
conda search cuda
```

检查当前的cudnn版本号:

```shell
conda search cudnn
```

在CUDA版本为9，cuDNN版本为7的要求下，你可以从中选择对应的版本进行安装。

这里提供一种安装方案。

```shell
conda install cudatoolkit=9.0
conda install cudnn=7.6.5
```

此方案不一定可行，仅供参考。

最后，我们使用pip安装tensorflow-gpu

```shell
pip install tensorflow-gpu==1.11
```

这一切都安装完成之后，你应该就能够正常使用TensorFlow了。

需要注意的是，如果你要在Windows环境下非Anaconda环境中使用GPU版本的TensorFlow的话，你可能需要对应安装CUDA 10才能够使用。

如果你是在Ubuntu环境下的话，你可以直接在Python中安装NVDIA相关包来实现，无需再手动安装其他版本的CUDA。

```shell
# To install the NVIDIA wheels for Tensorflow, install the NVIDIA wheel index
pip install nvidia-pyindex
# To install the current NVIDIA Tensorflow release
pip install --user nvidia-tensorflow[horovod]
```

总之，所有安装的代码罗列如下。

```shell
conda create -n tensorflow-gpu python=3.6
conda activate tensorflow-gpu
conda install cudatoolkit=9.0
conda install cudnn=7.6.5
pip install tensorflow-gpu=1.11
```

如果有一天你不想使用该环境了，你可以使用以下命令删除该虚拟环境。

首先，我们需要退出虚拟环境。

```shell
conda deactivate
```

然后，将虚拟环境以及其中所有的包删除即可。

```shell
conda remove -n tensorflow --all
```
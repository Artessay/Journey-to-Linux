## CMake

在Ubuntu中，采用 `sudo apt-get install cmake` 进行安装的CMake版本过低，因此，这里推荐采用仓库安装或是源码安装，并以CMake 3.31版本为例说明。

### 源码安装

访问 [CMake](https://cmake.org/download/) 官网，选择适合你操作系统的安装包进行下载并安装。

```sh
wget https://github.com/Kitware/CMake/releases/download/v3.31.3/cmake-3.31.3.tar.gz
```

解压压缩包

```sh
tar -zxvf cmake-3.31.3.tar.gz
cd cmake-3.31.3/
```

安装编译源码所需要的工具

```sh
sudo apt-get install libssl-dev
```

配置安装信息

```sh
./bootstrap --prefix=/usr/local/cmake
```

编译安装

```sh
make -j 64
sudo make install
```

添加环境变量

```sh
export PATH=/usr/local/cmake/bin:$PATH
```
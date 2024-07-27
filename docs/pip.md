## pip

### 配置

如果你采用`pip`来安装包的话，我们可以同时也将`pip`的源也修改为国内镜像站的镜像源，来加快python库的下载速度。

比如下方我们将`pip`源切换到浙江大学镜像站。

```shell
pip install pip -U
pip config set global.index-url https://mirrors.zju.edu.cn/pypi/web/simple
```

### 导出和导入依赖文件

pip提供了`pip freeze`和`pip install`命令，通过这两个命令可以导出和导入依赖文件。然而，`pip freeze`命令会将所有的依赖都导出，包括项目所没有使用到的依赖。

为了最小化导出依赖项，我们可以使用`pipreqs`的第三方库来导出项目所使用的依赖并生成`requirements.txt`文件。

由于`pipreqs`是第三方库，所以我们需要先安装`pipreqs`才能使用。

```shell
pip install pipreqs
```

随后，就可以通过`pipreqs`导出依赖项了。

```shell
pipreqs [project_path]
```

当我们需要在新的环境中重新安装相关依赖时，就可以通过`pip install -r requirements.txt`来安装。

### 常见问题

如果你在采用pip安装过程中因为缓存空间不足而被killed的话，你可以通过如下命令解决：

```shell
pip install -r requirements.txt --no-cache-dir
```
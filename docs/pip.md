## pip

### 配置

如果你采用`pip`来安装包的话，我们可以同时也将`pip`的源也修改为国内镜像站的镜像源，来加快python库的下载速度。

比如下方我们将`pip`源切换到浙江大学镜像站。

```shell
pip install pip -U
pip config set global.index-url https://mirrors.zju.edu.cn/pypi/web/simple
```

### 常见问题

如果你在采用pip安装过程中因为缓存空间不足而被killed的话，你可以通过如下命令解决：

```shell
pip install -r requirements.txt --no-cache-dir
```
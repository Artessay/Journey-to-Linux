## 离线安装

### vscode-server

首先，在VSCode中找到该版本的commit-id，随后，根据服务器架构下载vscode-server的客户端。

x86架构：

```sh
https://update.code.visualstudio.com/commit:${commit_id}/server-linux-x64/stable
```



arm架构：

```sh
https://update.code.visualstudio.com/commit:${commit_id}/server-linux-arm64/stable
```



例如，对于commit-id为`8b3775030ed1a69b13e4f4c628c612102e30a681` 的VSCode，下载x86架构vscode-server的地址为：

```sh
https://update.code.visualstudio.com/commit:8b3775030ed1a69b13e4f4c628c612102e30a681/server-linux-x64/stable
```



完成下载后，可以得到`vscode-server-linux-arm.tar.gz` 文件。

接着，将该文件传到离线服务器上，解压该文件并将其移动到`~/.vscode-server/bin/${commit-id}/`目录下便完成vscode-server的安装了。

```sh
tar -xvf vscode-server-linux-arm.tar.gz
cp -r vscode-server-linux-x64/* ~/.vscode-server/bin/${commit-id}/
```



如果仍然连接不上,则可能需要修改`.vscode-server`文件夹及其子目录的权限,例如权限改为`700`,再尝试连接:

```sh
chmod -R 700 /home/${user}/.vscode-server/
```



### conda

如果对应服务器没有网络，为了方便安装conda相关环境，我们就需要conda打包环境的能力。

首先，安装conda打包环境的相关工具

```sh
conda install -c conda-forge conda-pack
```



安装好之后打包需要迁出的环境，其中，`-n` 参数之后为虚拟环境名字，`-o` 参数之后为打包出来的文件名

```sh
conda pack -n [envsname] -o [conda_envsname].tar.gz
```



接下来，你可以将打包好的文件传输到离线服务器上。

完成传输后，我们首先在对应的conda目录下创建好对应环境名称的目录，如：

```sh
mkdir -p /home/${user}/miniconda3/envs/[envsname]
```



并解压环境到该目录下：

```sh
tar -xzf conda_envsname.tar.gz -C /home/${user}/miniconda3/envs/[envsname]
```



解压完成后，便完成conda环境的安装了。



### pip

在没有互联网连接的服务器上，可能需要离线安装`pip`包。为了解决这个问题，我们也需要在有网络的机器上先下载好相关包之后，再传送到离线服务器上。

首先，我们需要在有网络的机器上下载包和依赖。我们可以使用`pip`下载您想要的包，例如我们选择`requests`为例：

```python3
pip download requests
```

这将下载`requests`及其所有依赖到当前目录。

接下来，将下载的文件传输到目标机器使用USB驱动器、SCP、FTP或其他任何方法将在步骤1中下载的`.whl`和`.tar.gz`文件传输到离线的目标机器。

最后，我们要在目标机器上使用`pip`从本地文件安装包：

```text
pip install --no-index --find-links=./ requests
```

这告诉`pip`只从当前目录中查找文件，并且不要尝试在线查找。

需要注意的是，安装离线包的Python版本应与服务器的Python版本一致


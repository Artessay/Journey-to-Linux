## 网络管理

### SSH连接

为了能够实现与服务器之间的SSH连接，我们首先需要在服务器上安装openssh服务。相关命令如下：

```shell
sudo apt update
sudo apt install openssh-server

# show
sudo systemctl status ssh
```

如果最后你能够看到ssh服务的状态为正常启动了，那么此时你应该就可以正常使用SSH连接服务器了。

### SSH免密登录

将主机A的公钥（`id_sra.pub`）放到主机B的`authorized_keys`中，主机A即可免密登录主机B。

如果你还没有生成过SSH密钥对的话，你可以通过如下命令生成：

```shell
ssh-keygen
```

生成后，将公钥追加到主机B的`authorized_keys`就可以了。比如下方的操作可以让我们免密SSH登录到自己。

```shell
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

ssh localhost
```



如果此时你还不能实现免密登录，则可能是相关文件和目录的权限没有正确设置。你可以通过以下命令设置相应权限。

```sh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```


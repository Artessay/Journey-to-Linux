## 目录配置

有时候，我们home目录下的磁盘空间可能不够大，我们需要在额外的硬盘上存储相关数据。但我们又不希望每次都频繁切换目录。这时候，我们就需要做磁盘映射。

比如，我们服务器上的`/data`目录上具有很大的空间，我们将在这里建立我们自己的目录，并进行映射。

### 目录创建

你可能说，创建目录有什么难的，一条`mkdir`命令不就结束了吗？

确实如此，但有时，我们可能普通用户并没有该目录下的读写权限，我们只能用超级用户root来创建目录。但超级用户root创建的目录又只允许root读写，这样我们每次访问该目录时还需要加上sudo，就非常的不方便。所以，我们在创建目录后，还需要修改目录的读写权限，以让我们正常用户可以直接访问。

相关命令如下：

```sh
sudo mkdir -p /data/qrh
sudo chown -R <username>:<groupname> /data/qrh
```

将某个用户添加到分组中的命令如下

```sh
sudo usermod -G <groupname> <username>
```

### 创建链接

```sh
sudo mv /home/qrh /home/qrh.bak
sudo ln -s /data/qrh /home/qrh
```

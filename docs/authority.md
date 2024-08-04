## 权限管理

### 创建新用户

大多数时候，我们都需要与其他人共享一个服务器。如果只有一个账号的话，我们可能在使用安全性与权限管理上会比较困难一些。因此，我们往往需要创建不同的账号和用户组来满足不同的用户管理需求。

创建新用户需要在特权模式下进行，以下是新用户创建的相关命令。

```shell
sudo su # 切换到特权模式
adduser aloha # 添加新用户，这里，我们所设置的用户名为 aloha
passwd ****** # 为新用户设置密码
groupadd SSHD_USER # 创建一个新的组分类 SSHD_USER
usermod -G SSHD_USER aloha # 将用户 aloha 加入到新的分组 SSHD_USER
```

在上面的命令中，我们创建了一个新的用户`aloha`，和一个新的分组`SSHD_USER`，并将新用户加入到了该分组之中。

### 添加管理权限

如果你需要为一个普通用户添加sudo权限，你可以按照如下步骤进行操作。

**方法1：**

在有sudo权限的账号下直接使用如下命令添加：

```sh
sudo usermod -a -G sudo [username]
```

**方法2：**

首选，切换到root用户下。

```sh
su
```

接着，编辑sudoers文件。由于sudoers文件对root来说也只是只读的，所以，我们在编辑前需要先为其添加可写权限。

```sh
chmod u+w /etc/sudoers
```

然后编辑`/etc/sudoers`文件，在其中找到 `root ALL=(ALL) ALL`，在这行下放添加 `<username> ALL=(ALL) ALL`。其中，\<username\>是你想要添加sudo权限用户的用户名。

添加完成之后，sudoers文件局部大概类似这样：

```sh
# User privilege specification
root    ALL=(ALL:ALL) ALL
qrh     ALL=(ALL:ALL) ALL
```

在完成编辑之后，记得撤销sudoers文件的写权限，以免发生不必要的意外。

```sh
chmod u-w /etc/sudoers
```

### SSHD权限管理

建立分组的目的是为了能够更加精细的实现权限控制。下面我们以修改`SSHD_USER`相关分组权限的示例来说明相关操作。

与SSHD相关的配置文件在`/etc/ssh/sshd_config`目录下，我们可以通过vim或者其他工具对其进行编辑。如：

```shell
vim /etc/ssh/sshd_config
```

在文件中，我们可以寻找以下内容进行修改，或在文件末尾加入下述内容完成配置：

```t
# 允许登录的组: SSHD_USER ec2-user
AllowGroups SSHD_USER root ec2-user
 
# 仅允许 SSHD_USER 组使用密码登录
Match Group SSHD_USER
    PasswordAuthentication yes
```

完成配置修改后，我们需要重启SSHD服务才能应用最新的更改。

```shell
systemctl restart sshd
systemctl status sshd
```
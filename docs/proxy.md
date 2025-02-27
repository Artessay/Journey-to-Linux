## SSH隧道

### 符号定义

主机A：端口->a_port，访问IP:a_host，用户：a_user

主机B：端口->b_port，访问IP:b_host，用户：b_user

主机C：端口->c_port，访问IP:c_host，用户：c_user

### 正向代理

条件：主机A没法直接访问主机C，但是**主机A可以访问主机B，主机B可以访问主机C**。

目标：主机A访问主机C。

方法：在主机A上执行以下命令：

```bash
ssh -L a_port:c_host:c_port b_user@b_host 
```

### 反向代理

条件：主机A没法直接访问主机C，但是**主机C可以访问主机B，主机A也可以访问主机B**。

目标：主机A访问主机C。

方法：在主机C执行以下命令：

```bash
ssh -R b_port:c_host:c_port b_user@b_host
```

## 内网穿透

上述正向代理与反向代理的命令都是依靠临时建立的SSH连接来实现暂时的内网穿透。但更多时候，我们需要一个稳定的内网穿透方法，以便我们可以在外网的环境中，通过一台在公网中具有固定IP地址的云服务器来访问内网中的资源。

这里，我们在上述理论的基础上，通过AutoSSH的方法，实现可靠持久的内网穿透。

### 方法步骤

设备准备：一台内网主机 A，一台 Linux 公网主机 B

**第一步：公网服务器配置**

​修改公网主机 B 的 SSH 配置文件`/etc/ssh/sshd_config`，将其中的GatewayPorts的设置改为yes。默认应该是用注释带起的`#GatewayPorts no`。需要注意，该修改需要管理员的sudo权限才能完成。

我们可以采用vim打开该文件。如果你担心随意修改导致最终SSHD不可用的话，你可以在修改之前先将相关文件进行备份，以方便后续随时可以恢复“出厂设置”。

```shell
sudo cp sshd_config sshd_config.bak
sudo vim sshd_config
```

随后，将该文件的内容按以下修改：

```shell
GatewayPorts yes
```

 这样修改后，就可以把监听的端口绑定到任意 IP 0.0.0.0 上，否则只有本机 127.0.0.1 可以访问。

 完成修改后，我们还需要重启SSHD服务。

 ```shell
 sudo service sshd restart
 ```

 **第二步：内网服务器安装 AutoSSH 服务**

 在内网主机 A 上，执行以下命令安装 AutoSSH

 ```shell
 sudo apt install autossh
 ```

**第三步：断线免密登录自动重连**

​ssh 反向链接会因为超时而关闭，如果关闭了那从外网连通内网的通道就无法维持。

因为 autossh 是自动运行，不可能有人输入密码，因此需要用密钥文件认证。

为此我们需要结合免密码登录及 AutoSSH 来提供稳定的 ssh 反向代理隧道。

​ 1、在内网主机 A 上产生公钥和私钥

```sh
ssh-keygen
```

然后按三次回车执行默认选项生成公钥和私钥。会生成密钥文件和私钥文件 id_rsa,id_rsa.pub 或 id_dsa,id_dsa.pub

​ 2、拷贝秘钥 在内网主机 A 上继续执行如下命令，将内网主机 A 上的秘钥文件 copy 到公网主机 B 中。

```sh
ssh-copy-id  username@ip
```

或者

```sh
ssh-copy-id -p <ssh_port_a> <user_a>@<host_a>
```

其中“username”是公网主机 B 的用户名，ip 为公网主机 B 的 ip。如果公网主机的SSH连接不是常规的22端口的话，我们还需要指定端口号。

该命令执行后按照提示输入公网主机 B 的密码就完成了。

**第四步：利用 AutoSSH 实现端口转发**

在内网主机 A 上，利用 AutoSSH 建立一条 SSH 隧道

```sh
autossh -M 5842 -NR 23104:localhost:8976 username@xxx.xxx.xxx.xxx (-p xxxx)
```

参数解释：
* “-M 5842”意思是使用内网主机 A 的 5842 端口监视 SSH 连接状态，连接出问题了会自动重连
* “-N”意思是不执行远程命令
* “-R”意思是将远程主机（公网主机 B）的某个端口转发到本地指定机器的指定端口，即反向代理

命令解释：
* “23104:localhost:8976”意思是将内网主机 A 的 8976 号端口转发至公网主机 B 的 23104 号端口上
* “username@xxx.xxx.xxx.xxx”意思是公网主机 B 的用户名和 IP
* “-p xxxx”意思是公网主机 B 的 SSH 端口，如果是默认的 22 号端口，则可以不输入

**第五步：监听端口检查**

​分别检查内网主机 A 及公网主机 B 的端口监听情况，出现如下进程则为正常。

​内网主机 A：

```sh
[root@localhost ~]# lsof -i:5842
COMMAND   PID USER   FD   TYPE    DEVICE SIZE/OFF NODE NAME
ssh     33080  qrh    4u  IPv6 248965747      0t0  TCP localhost:5842 (LISTEN)
ssh     33080  qrh    5u  IPv4 248965748      0t0  TCP localhost:5842 (LISTEN)
```

公网主机 B：

```sh
[root@localhost ~]# lsof -i:23104
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
sshd    9762 root   10u  IPv4 473994      0t0  TCP *:webcache (LISTEN)
sshd    9762 root   11u  IPv6 473995      0t0  TCP *:webcache (LISTEN)
```

有时候，我们可能会希望该服务能够实现开机自启动。这里有许多不同的方法。

比如，你可以采用添加服务的方法，创建文件`/lib/systemd/system/autossh.service`，并输入以下内容：

```sh
#file path /lib/systemd/system/autossh.service
 
[Unit]
Description=autossh
Wants=network-online.target
After=network-online.target
 
[Service]
Type=simple
Environment="AUTOSSH_GATETIME=0"
User=<user_b>
Group=<user_b>
WorkingDirectory=/home/<user_b>
ExecStart=/usr/bin/autossh -M 0 -p <port_a> -NR <virtual_port>:localhost:<port_b> <user_a>@<host_a>
 
[Install]
WantedBy=multi-user.target
```

然后在系统中注册启动服务：

```sh
systemctl enable autossh
```

在CentOS系统中，也可以采用修改`/etc/rc.d/rc.local`的方式，在其中添加以下内容来实现。

```sh
autossh -M 5842 -NR 23104:localhost:8976 username@xxx.xxx.xxx.xxx (-p xxxx)
chmod +x /etc/rc.d/rc.local
```

其他方式还有很多，这边就不一一列举了。

## 注意事项

在通信过程中，需要注意双方使用的协议一致。如果你采用Python程序编写服务器和客户端，并且直接使用了其中的库进行通信的话，那么不同版本之间的程序可能是无法直接进行通信的。比如，Python 3.8+ 版本的协议与更低版本的协议就不兼容。Python 3.8+使用的协议号为5. 不同版本之间通信可能会产生如下报错：`ValueError: unsupported pickle protocol: 4`

## 示例程序

见Github仓库 [NAT Traversal](https://github.com/Artessay/NAT-Traversal)，里面包含了采用Python程序编写的简单的Client，Middle，以及Server的示例代码。你在修改了`client_config.example.py`的IP地址，并将其重命名为`client_config.py`之后，应该可以直接运行。


## frp连接

### 安装和部署

可以参考[frp文档](https://gofrp.org/zh-cn/docs/)安装和部署。

### 开机自动启动

创建frpc的服务文件

```bash
sudo vim /etc/systemd/system/frpc.service
```

在文件中写入以下内容：

```bash
[Unit]
# Service Description
Description=frpc service 
After=network.target syslog.target
Wants=network.target

[Service]
Type=simple
# Execution Command
ExecStart=/path/to/frp/frpc -c /path/to/frp/frpc.toml

[Install]
WantedBy=multi-user.target
```

重载`systemctl`

```bash
sudo systemctl daemon-reload
```

设置开机自动启动

```bash
sudo systemctl enable frpc
```

使用`systemctl`命令管理frpc

```bash
#启动
sudo systemctl start frpc 
#关闭
sudo systemctl stop frpc
#重启
sudo systemctl restart frpc
#查看状态
sudo systemctl status frpc
```
## screen

### 简介与安装

你是否遇到过这种情况：辛辛苦苦训练的模型还有两分钟就训练完了，这时因为网络不稳定和服务器的链接突然断掉，导致你训练的代码也被强行终止。只能重新训练？

我们通过ssh连接的服务器是十分脆弱的。稍微一些不稳定的因素都会断连导致训练终止。而`screen`则是解决这一问题的一个非常好的方法。

在ubuntu下安装：

```shell
sudo apt install screen
```

screen可以打开一个窗口，在这个窗口下执行你的操作，比如训练模型。 如果这时突然断开和服务器的连接，别担心。重新连接上去，打开那个screen窗口，你会发现你的代码还在安全的运行中。

### 使用方法与常见命令

**新建screen session**

```shell
screen -S yourname
```

youname 是session的名字。这个很重要，要取一个好区分的名字。

新建完session后，你会进入到这个session里，此时就可以在这里训练你的模型。

**当你和服务器断连后，如何重新连接？**

**查看session**

```shell
 screen -ls
```

它会返回的结果类似下面这样：

```shell
There are screens on:
        563939.train    (10/28/2023 01:32:13 AM)        (Attached)
        3306477.refine  (10/21/2023 04:22:28 AM)        (Detached)
2 Sockets in /run/screen/S-qiurihong.
```

这里会显示你的相关session。Attached表示这些session都还是打开在屏幕上。断连之后的session的状态则会变为Detached。

**Detach**

```text
screen -D 563939.train
```

注意这里指明的session需要包括前面的数字，一个小数点和后面的session name。

当你全部detach完后，就可以重新打开你的session。

**Resume**

```text
screen -r refined
```

注意此时只需要指明session name即可。

此时你就会看到之前训练的代码正在安全的运行中~

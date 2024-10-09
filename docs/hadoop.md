## Hadoop

### 安装与配置

截止本文撰写时，Apache Hadoop 3.3 and upper supports Java 8 and Java 11 (runtime only)，对于更高版本的Java则暂不支持。因此，我们需要首先安装Java 8 或者 Java 11。本文以Java 11 和 Hadoop 3.3.6版本的安装与配置为例，准备Hadoop所需要的Java环境。

#### 环境准备

* **Java**: Hadoop 依赖于Java，首先需要确保安装了Java。

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
java -version
```

* **SSH**: Hadoop需要SSH访问自己，即便是单节点部署。

```bash
sudo apt install openssh-server -y
ssh-keygen -t rsa -P "" -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
ssh localhost
```

#### 下载Hadoop

从Apache Hadoop官方网站下载最新的Hadoop版本，解压后放入`/usr/local/hadoop`目录下。

```bash
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
tar -xzvf hadoop-3.3.6.tar.gz
sudo mv hadoop-3.3.6 /usr/local/hadoop
```

#### 配置环境变量

配置Java环境变量，在`~/.bashrc`文件中添加如下内容：

```bash
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
```

配置Hadoop环境变量，在`~/.bashrc`文件中添加如下内容：

```bash
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

应用配置：

```bash
source ~/.bashrc
```

#### 配置Hadoop

在`/usr/local/hadoop/etc/hadoop/`目录中，修改以下配置文件：

* **hadoop-env.sh**: 指定JAVA_HOME以及其他所需的环境变量

```sh
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export HADOOP_SSH_OPTS="-p <your-ssh-port>" # 如果使用非默认的SSH端口，需要修改
```

* **core-site.xml**: 设置HDFS的namenode地址。

```xml
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

* **hdfs-site.xml**: 设置HDFS的节点数量。

```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

* **mapred-site.xml**: 指定JobTracker的地址

```xml
<configuration>
    <property>
        <name>mapred.job.tracker</name>
        <value>localhost:9010</value>
    </property>
</configuration>
```

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

配置完成后，可以使用如下命令检查环境变量是否配置正确：

```bash
hadoop version
```

#### 配置Hadoop

在`/usr/local/hadoop/etc/hadoop/`目录中，修改以下配置文件：

* **hadoop-env.sh**: 指定JAVA_HOME以及其他所需的环境变量

```sh
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
```

```sh
# 如果localhost使用非默认的SSH端口，需要修改
export HADOOP_SSH_OPTS="-p <your-ssh-port>" 
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

启动Hadoop集群：

```bash
./start-dfs.sh
./start-yarn.sh
```

此时，可以通过如下命令查看启动情况。如出现namenode、datanode、resourcemanager、nodemanager等进程，则表示Hadoop集群启动成功。

```bash
jps
```

### WordCount示例

我们以WordCount程序示例为例，演示如何使用Hadoop完成分布式计算。

#### 创建JAVA程序

创建一个名为`WordCount.java`的文件，并写入以下代码：

```java
import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

  public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  public static class IntSumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values, Context context)
        throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```

#### 编译并打包

确保你在包含Hadoop库的环境中编译程序。首先设置classpath，然后编译：

```bash
export HADOOP_CLASSPATH=$HADOOP_HOME/share/hadoop/common/hadoop-common-3.3.6.jar:$HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-client-core-3.3.6.jar
javac -classpath $HADOOP_CLASSPATH -d wordcount_classes WordCount.java
jar -cvf wordcount.jar -C wordcount_classes/ .
```

#### 运行WordCount程序

上传输入文件到HDFS：

```bash
hdfs dfs -mkdir /input
hdfs dfs -put /path/to/local/inputfile.txt /input
```

运行WordCount程序：

```bash
hadoop jar wordcount.jar WordCount /input /output
```

查看输出结果：

```bash
hdfs dfs -cat /output/part-r-00000
```

输出结果是一个单词和该单词出现的次数的键值对。
这样，便完成了在Linux上部署Hadoop，并运行一个简单的WordCount程序了。

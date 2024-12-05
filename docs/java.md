## JDK 21

### Online Installation

```sh
sudo apt-get update
sudo apt-get install openjdk-21-jdk
```

### Offline Installation

Download JDK:

```sh
wget https://download.oracle.com/java/21/latest/jdk-21_linux-x64_bin.tar.gz
```

Create folder for Java:

```sh
mkdir -p /usr/local/java
```

Extract Java:

```sh
tar -zxvf jdk-21_linux-x64_bin.tar.gz -C /usr/local/java
```

Add Java to PATH in `/etc/profile`:

```sh
JAVA_HOME=/usr/local/java/jdk-21.0.4
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME PATH
```

## Maven 3.9.8

Download Maven

```sh
wget https://dlcdn.apache.org/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz
```

Create folder for Maven:

```sh
mkdir -p /usr/local/maven
```

Extract Maven:

```sh
tar -zxvf apache-maven-3.9.8-bin.tar.gz -C /usr/local/maven
```

Add Maven to PATH in `/etc/profile`:

```sh
MAVEN_HOME=/usr/local/maven/apache-maven-3.9.8
PATH=$MAVEN_HOME/bin:$PATH
export MAVEN_HOME PATH
```

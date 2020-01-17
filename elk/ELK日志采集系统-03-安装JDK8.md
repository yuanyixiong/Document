# 安装JDK8
## 下载JDK
1. jdk-8u191-linux-x64.tar.gz
## 解压JDK
## 配置环境变量
1. 初始化安装目录
```shell
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```
2. 设置JAVA_HOME、CLASSPATH环境变量
```shell
echo "=================设置JAVA_HOME、CLASSPATH环境变量================="
echo "

#新建环境变量 JAVA_HOME
export JAVA_HOME=$app/jdk1.8.0_191
#追加环境变量CLASSPATH 识别JDK lib目录
export CLASSPATH=.:\$CLASSPATH:$JAVA_HOME/lib
#追加环境变量PATH 识别JDK bin 目录
export PATH=\$PATH:\$JAVA_HOME/bin

" > $config/java
```
3. 追加进用户环境变量
```
echo "=================追加进用户环境变量================="
cat $config/java >> ~/.bashrc
source ~/.bashrc
```
## 测试安装
```
[root@slave3 app]# java -version
java version "1.8.0_191"
Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)
```
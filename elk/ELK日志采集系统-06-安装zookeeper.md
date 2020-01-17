# 安装zookeeper
## 初始化安装目录
```
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```

## 导入安装zookeeper
```
cp /mnt/hgfs/服务器现有资料/zookeeper-3.4.11.tar $files
tar -xvf $files/zookeeper-3.4.11.tar -C $app
```

## 配置zookeeper目录结构
* 集群下 myid 内容必须不同
```
echo "=================配置zookeeper目录结构================="

zookeeper_root=$app/zookeeper-3.4.11
rm -rf $zookeeper_root/zk
mkdir -p $zookeeper_root/zk/log
mkdir -p $zookeeper_root/zk/data
touch $zookeeper_root/zk/data/myid
echo "1"> $zookeeper_root/zk/data/myid

```

## 配置zookeeper集群配置
```
echo "=================配置zookeeper集群配置文件================="
zookeeper_root=$app/zookeeper-3.4.11
touch $zookeeper_root/conf/zoo.cfg

echo "

tickTime=2000
initLimit=10
syncLimit=5

dataDir=$zookeeper_root/zk/data
dataLogDir=$zookeeper_root/zk/log

clientPort=2181

server.1=172.16.89.150:2888:3888
server.2=172.16.89.151:2888:3888
server.3=172.16.89.152:2888:3888
server.4=172.16.89.153:2888:3888

" >> $zookeeper_root/conf/zoo.cfg
```

## 启动zookeeper
* zookeeper集群正常运行节点过半,整个集群才是健康状态
```
echo "=================启动zookeeper================="
zookeeper_root=$app/zookeeper-3.4.11
sh $zookeeper_root/bin/z

kServer.sh start $zookeeper_root/conf/zoo.cfg
```

## 查看zookeeper状态
* zookeeper集群正常运行节点过半,整个集群才是健康状态
```
echo "=================查看zookeeper================="
zookeeper_root=$app/zookeeper-3.4.11
sh $zookeeper_root/bin/zkServer.sh status $zookeeper_root/conf/zoo.cfg
```

## 停止zookeeper
```
echo "=================停止zookeeper================="
zookeeper_root=$app/zookeeper-3.4.11
sh $zookeeper_root/bin/zkServer.sh stop $zookeeper_root/conf/zoo.cfg
```
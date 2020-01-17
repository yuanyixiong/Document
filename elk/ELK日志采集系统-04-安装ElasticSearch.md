# 安装ElasticSearch
## 初始化安装目录
```shell
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```
## 导入安装elasticsearch
```shell
echo "=================安装elasticsearch================="
cp /mnt/hgfs/服务器现有资料/elasticsearch-6.6.1.tar.gz $files
tar -xvf $files/elasticsearch-6.6.1.tar.gz -C $app
```
## 调整Linux 系统参数
* elasticsearch 启动会报错
```shell
。。。。。。省略其他启动信息
ERROR: [3] bootstrap checks failed
[1]: max file descriptors [65535] for elasticsearch process is too low, increase to at least [65536]
[2]: max number of threads [3895] for user [elastic] is too low, increase to at least [4096]
[3]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[2018-03-08T19:29:32,233][INFO ][o.e.n.Node               ] [s3f1uD4] stopping ...
[2018-03-08T19:29:32,397][INFO ][o.e.n.Node               ] [s3f1uD4] stopped
[2018-03-08T19:29:32,397][INFO ][o.e.n.Node               ] [s3f1uD4] closing ...
[2018-03-08T19:29:32,412][INFO ][o.e.n.Node               ] [s3f1uD4] closed
```

1. 启动报错：max file descriptors [65535] for elasticsearch process is too low, increase to at least [65536]
```shell
echo "=================vim /etc/security/limits.conf================="
vim /etc/security/limits.conf
*               hard    nofile          65536
```
2. 启动报错：max number of threads [3895] for user [elastic] is too low, increase to at least [4096]
```shell
echo "=================vim /etc/security/limits.conf================="
vim /etc/security/limits.conf
*               hard    nproc           4096
```
3. 启动报错：max number of threads [3895] for user [elastic] is too low, increase to at least [4096]
```shell
echo "=================/etc/security/limits.d/90-nproc.conf================="
vim /etc/security/limits.d/90-nproc.conf
*          soft    nproc     4096
```
4. 启动报错：max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```shell
echo "=================/etc/sysctl.conf================="
vim /etc/sysctl.conf
vm.max_map_count=262144

echo "=================校验属性添加================="
sysctl -p
```
## 调整elasticsearch jvm运行参数
```shell
echo "=================手动更改vim ./config/jvm.options================="
vim $app/elasticsearch-6.6.1/config/jvm.options
#修改内容
-Xms200m
-Xmx200m
```
## 配置elasticsearch 集群

### IP获取
```shell
echo "=================获取本机ip================="
ip=$('ifconfig'| grep 'Mask:255.255.255.0' -A 0 | awk -F' ' '{print $2}' | awk -F':' '{print $2}')
```
### 主节点配置
```shell
elasticsearch_root=$app/elasticsearch-6.6.1

echo "=================配置主节点================="
elasticsearch_root=$app/elasticsearch-6.6.1

echo "


#解决插件和elasticsearch的跨域问题
http.cors.enabled: true
http.cors.allow-origin: \"*\"
bootstrap.memory_lock: false
bootstrap.system_call_filter: false

#节点
cluster.name: app #集群名
node.name: master #节点名
node.master: true #是否为主节点
node.data: false #是否是数据节点
network.host: $ip #配置ip，默认9200
http.port: 9200

#集群
discovery.zen.ping.unicast.hosts: [\"172.16.89.150\", \"172.16.89.151\", \"172.16.89.152\", \"172.16.89.153\"] # 配置自动发现


" >> $elasticsearch_root/config/elasticsearch.yml
```

### 从节点配置
```shell
elasticsearch_root=$app/elasticsearch-6.6.1

echo "


#解决插件和elasticsearch的跨域问题
http.cors.enabled: true
http.cors.allow-origin: \"*\"
bootstrap.memory_lock: false
bootstrap.system_call_filter: false

#节点
cluster.name: app #集群名
node.name: salve1 #节点名
node.master: fasle #是否为主节点
node.data: true #是否是数据节点
network.host: $ip #配置ip，默认9200
http.port: 9200

#集群
discovery.zen.ping.unicast.hosts: [\"172.16.89.150\", \"172.16.89.151\", \"172.16.89.152\", \"172.16.89.153\"] # 配置自动发现


" >> $elasticsearch_root/config/elasticsearch.yml
```

## 启动 elasticsearch
* 不能使用root用户操作
1. 新建es用户,并授权
```shell
useradd es
chown es:es -R $app/elasticsearch-6.6.1
su es
```
2. 启动elasticsearch
```
echo "=================启动================="
sh /usr/local/src/app/elasticsearch-6.6.1/bin/elasticsearch -d
```
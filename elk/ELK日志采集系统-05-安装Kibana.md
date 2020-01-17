# 安装Kibana
## 初始化安装目录
```shell
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```
## 导入安装Kibana
```shell
echo "=================安装kibana================="
cp /mnt/hgfs/服务器现有资料/kibana-6.6.1-linux-x86_64.tar.gz $files
tar -xvf $files/kibana-6.6.1-linux-x86_64.tar.gz -C $app
```
## 配置Kibana
```
echo "=================配置kibana================="
kibana_root=$app/kibana-6.6.1-linux-x86_64
cat $kibana_root/config/kibana.yml

echo "

server.port: 5601                             # 配置kibana的端口
server.host: 172.16.89.150                    # 配置监听ip
elasticsearch.url: http://172.16.89.150:9200  # 配置es服务器的ip，如果是集群则配置该集群中主节点的ip
logging.dest: /var/log/kibana.log             # 配置kibana的日志文件路径，不然默认是messages里记录日志

" >> $kibana_root/config/kibana.yml

echo "=================创建日志文件================="
touch /var/log/kibana.log; 
chmod 777 /var/log/kibana.log
```
## 启动Kibana
```
echo "=================启动kibana================="
kibana_root=$app/kibana-6.6.1-linux-x86_64
nohup $kibana_root/bin/kibana &
```

## 检查Kibana运行
```
echo "=================查看kibana运行================="
ps aux | grep kibana
netstat -lntp |grep 5601
```
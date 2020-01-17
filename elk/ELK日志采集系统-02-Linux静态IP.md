# ELK Linux 集群环境搭建
## Linux 网络设置
### VM 子网,网关设置
1. VMent8
2. NAT 模式
3. 子网IP：172.16.89.1
4. 子网掩码： 255.255.255.0
5. 网关：172.16.89.2

### 联网设置静态IP
1. 初始化安装目录
```shell
echo "=================初始化安装目录================="
app=/usr/local/src/app
config=/usr/local/src/config
files=/usr/local/src/files
```
2. 静态网络设置
```shell
echo "=================静态网络设置================="
echo "

DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
NM_CONTROLLED=yes
BOOTPROTO=static
IPADDR=172.16.89.150
NETMASK=255.255.255.0
BROADCAST=192.168.106.255
GATEWAY=172.16.89.2
DNS1=114.114.114.114
DNS2=8.8.8.8
DEFROUTE=yes
IPV4_FAILURE_FATAL=yes
IPV6INIT=no
PEERDNS=yes
PEERROUTES=yes
" >> $config/ifcfg-eth0
```
3. 覆盖默认网络设置
```
echo "=================覆盖默认网络设置================="
cat $config/ifcfg-eth0 > /etc/sysconfig/network-scrpits/ifcfg-eth0
```
4. 重启网络
```shell
echo "=================重启网络================="
[root@master app]# /etc/init.d/network restart
Shutting down interface eth0:  Device state: 3 (disconnected)
                                                           [  OK  ]
Shutting down loopback interface:                          [  OK  ]
Bringing up loopback interface:                            [  OK  ]
Bringing up interface eth0:  Active connection state: activated
Active connection path: /org/freedesktop/NetworkManager/ActiveConnection/3
                                                           [  OK  ]
```
5. 检查网络
```shell
echo "=================检查网络:IP检查================="
[root@master app]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:0C:29:21:C5:49  
          inet addr:172.16.89.150  Bcast:172.16.89.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe21:c549/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:8062239 errors:0 dropped:0 overruns:0 frame:0
          TX packets:6407611 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:1680446199 (1.5 GiB)  TX bytes:1111167919 (1.0 GiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:1717671 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1717671 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:388712368 (370.7 MiB)  TX bytes:388712368 (370.7 MiB)
          
echo "=================检查网络:网络通信检查================="
[root@master app]# curl www.baidu.com
<!DOCTYPE html>...</html>
```
### 多台Linux 使用 ssh免密登陆
1. 多台Linux 生成公钥
```shell
[root@master app]# ssh-keygen
[root@master app]# cd ~/.ssh/
[root@master app]# touch authorized_keys
[root@master app]# cat id_rsa.pub > authorized_keys
[root@master app]# authorized_keys 填写所有需要免密登陆linux的公钥
[root@master app]# scp -rp authorized_keys 172.16.89.151:~/.ssh
[root@master app]# ssh 172.16.89.151
```
2. ip 映射别名
```
echo "================= ip 映射别名================="
echo "
172.16.89.150 master
172.16.89.151 slave1
172.16.89.152 slave2
172.16.89.153 slave2
" >> $config/hosts
cat $config/hosts > /etc/hosts
```
3.测试ssh免密登录
```
[root@master app]# ssh salve1
[root@salve1 app]# ssh salve2
[root@salve2 app]# ssh salve3
[root@salve3 app]# ssh master
[root@master app]#
```

4. 修改主机名
```shell
echo "================= 修改主机名 ================="
cat "hostname=master" > /etc/sysconfig/network
```

### 防火墙
1. 防火墙状态
```
echo "=================防火墙状态================="
/etc/init.d/iptables status
getenforce
```
2. 关闭防火墙
```
echo "=================关闭防火墙================="
/etc/init.d/iptables stop
setenforce 0
```
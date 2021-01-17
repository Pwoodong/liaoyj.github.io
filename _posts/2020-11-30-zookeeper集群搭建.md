## Zookeeper集群搭建

### 系统环境准备

| 系统平台     | IP            | 备注 |
| ------------ | ------------- | ---- |
| CentosOS 8.2 | 192.168.1.120 |      |
| CentosOS 8.2 | 192.168.1.145 |      |
| CentosOS 8.2 | 192.168.1.211 |      |

### 下载并解压

​	https://zookeeper.apache.org/releases.html

### 创建目录及子目录

```
#创建目录及子目录
mkdir /data/zookeeper
cd zookeeper
mkdir conf
mkdir logs
#创建观察者目录
mkdir /data/zookeeper_obs
cd zookeeper_obs
mkdir conf
mkdir logs
```

在conf目录下创建配置文件并修改配置

```
touch zoo_sample.cfg
```

```
dataDir=/data/zookeeper
clientPort=2181
dataLogDir=/data/zookeeper/logs
server.1=192.168.1.211:2180:3180
server.2=192.168.1.145:2180:3180
server.3=192.168.1.120:2180:3180
server.4=192.168.1.211:2179:3179:observer
```

在主目录下创建myid文件，值对应配置文件的服务值

上述步骤需要在三台服务器上都执行，观察者只需要在一台机器上部署就行

### 启动

```
#进入zk安装目录，执行
cd /usr/local/zookeeper-3.6.1/bin
./zkServer.sh start
```
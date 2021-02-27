## Docker安装TiDB集群

Docker搭建TiDB集群和官方安装步骤差不多一样，具体可参考上一篇博文，此篇只把需要的具体命令记录下来

### 安装PD命令

```
docker run -d --name pd1 \
  -p 2379:2379 \
  -p 2380:2380 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data:/data \
  pingcap/pd:latest \
  --name="pd1" \
  --data-dir="/data/tidb/pd1" \
  --client-urls="http://0.0.0.0:2379" \
  --advertise-client-urls="http://192.168.1.120:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --advertise-peer-urls="http://192.168.1.120:2380" \
  --initial-cluster="pd1=http://192.168.1.120:2380,pd2=http://192.168.1.145:2380,pd3=http://192.168.1.211:2380"
  
 docker run -d --name pd2 \
  -p 2379:2379 \
  -p 2380:2380 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data:/data \
  pingcap/pd:latest \
  --name="pd2" \
  --data-dir="/data/tidb/pd2" \
  --client-urls="http://0.0.0.0:2379" \
  --advertise-client-urls="http://192.168.1.145:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --advertise-peer-urls="http://192.168.1.145:2380" \
  --initial-cluster="pd1=http://192.168.1.120:2380,pd2=http://192.168.1.145:2380,pd3=http://192.168.1.211:2380"
  
 docker run -d --name pd3 \
  -p 2379:2379 \
  -p 2380:2380 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data:/data \
  pingcap/pd:latest \
  --name="pd3" \
  --data-dir="/data/tidb/pd3" \
  --client-urls="http://0.0.0.0:2379" \
  --advertise-client-urls="http://192.168.1.211:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --advertise-peer-urls="http://192.168.1.211:2380" \
  --initial-cluster="pd1=http://192.168.1.120:2380,pd2=http://192.168.1.145:2380,pd3=http://192.168.1.211:2380"
```

### 安装TiKV命令

```
docker run -d --name tikv1 \
  -p 20160:20160 \
  --ulimit nofile=1000000:1000000 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data:/data \
  pingcap/tikv:latest \
  --addr="0.0.0.0:20160" \
  --advertise-addr="192.168.1.120:20160" \
  --data-dir="/data/tidb/tikv1" \
  --pd="192.168.1.120:2379,192.168.1.145:2379,192.168.1.211:2379"
  
docker run -d --name tikv2 \
  -p 20160:20160 \
  --ulimit nofile=1000000:1000000 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data:/data \
  pingcap/tikv:latest \
  --addr="0.0.0.0:20160" \
  --advertise-addr="192.168.1.145:20160" \
  --data-dir="/data/tidb/tikv2" \
  --pd="192.168.1.120:2379,192.168.1.145:2379,192.168.1.211:2379"  
  
docker run -d --name tikv3 \
  -p 20160:20160 \
  --ulimit nofile=1000000:1000000 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data:/data \
  pingcap/tikv:latest \
  --addr="0.0.0.0:20160" \
  --advertise-addr="192.168.1.211:20160" \
  --data-dir="/data/tidb/tikv3" \
  --pd="192.168.1.120:2379,192.168.1.145:2379,192.168.1.211:2379"  
```

### 安装TiDB命令

```
docker run -d --name tidb \
  -p 4000:4000 \
  -p 10080:10080 \
  -v /etc/localtime:/etc/localtime:ro \
  pingcap/tidb:latest \
  --store=tikv \
  --path="192.168.1.120:2379,192.168.1.145:2379,192.168.1.211:2379"
  
docker run -d --name tidb \
  -p 4000:4000 \
  -p 10080:10080 \
  -v /etc/localtime:/etc/localtime:ro \
  pingcap/tidb:latest \
  --store=tikv \
  --path="192.168.1.120:2379,192.168.1.145:2379,192.168.1.211:2379"  
```


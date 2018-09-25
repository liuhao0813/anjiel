### Centos7防火墙

CentOS7默认安装了firewalld防火墙

利用防火墙，我们可以允许或是限制传输的数据通过



程序  ---> 防火墙---->互联网

##### 管理防火墙

firewall-cmd  --state  查看防火墙状态

service firewall start  /stop /restart

##### 端口管理

firewall-cmd  --permanent --add-port 开发端口

firewall-cmd  --permanent --add-port=8080-8085/tcp

firewall-cmd --reload  重新加载

firewall-cmd  --permanent  --remove-port=8080-8085/tcp   删除端口，注意和添加的端口规则一样

firewall-cmd   --permanent  --list-ports 查看开放的端口列表

firewall-cmd --permanent --list-services  查看哪些服务使用了那些端口



docker save java > /homt/java.tar.gz   到处指定的镜像

docker load  <  /home/java.tar.gz  导入指定的镜像

--privileged  docker在运行的时候是有最高权限的  

docker tag  修改Image的名字



#### 常见的MYSQL 集群方案

Replication   数据是单向的，主从方案   采用异步复制， 无法保证数据的一致性

PXC   数据是双向同步，在任何一个节点上都可以读取数据

同步复制，事务在所有集群节点要么同时提交，要么不提交，强事务一致性 

docker network create --driver=overlay --attachable mynet 



docker swarm init --advertise-addr 192.168.1.17

创建完集群，会添加来你两个网络，一个是ingress 和docker_gwbridge 

docker node ls  查看当前节点的状态

docker node promote  节点名称 在manager节点提升worker节点为manager节点

docker node promote swarm-worker  提升worker节点swarm-worker 为manager节点

将所有worker 节点都指定为manager节点，这样swarm集群就是一个高可用状态

docker service create --name test alipine ping www.baidu.com

docker service logs test1  查看容易的日志

docker service create --name nginx --dethch=false nginx 

docker service update --publish-add 8080:80  --detach=false nginx  修改容器，添加端口的映射

netstat  -na |grep LISTEN 

docker service scale nginx=3 --detach=false



#### 自定义网络

docker network create -d overlay imooc-overlay 

docker network ls

docker service create --network imooc-overlay --name nginx -p 8080:80 --detach=false nginx

docker service create --network immoc-overlay --name alpine --name alipine  --detach =false alipine ping www.baidu.com

docker exec -it dddd23e sh 

docker service inspect nginx 

docker service inspect alpine 



#### dnsrr的方式

docker service create --name nginx-b --endpoint-mode dnsrr --detach=false nginx 

 docker service update  --network-add imooc-overlay --detach=fasel nginx-b 




# DNS

## Bind服务

### BIND 

开源，稳定，应用广泛的DNS服务

### 组成

1. 域名解析服务
2. 权威域名服务
3. DNS工具

DNS的域名

www.imooc.com   根域

域名的解析过程

DNS解析记录分类

A记录  域名对应的IP ，权威记录

CNAME  别名

MX记录，  针对邮件服务解析，配置A记录进行

NS记录   转发记录

- 什么是DNS及其作用
- 域名的空间结构
- 域名的解析过程



BIND安装

 yum  install -y bind bind-chroot

rpm -qa -ql 

systemctl start named  

配置文件位置/etc/named.conf

options()整个BIND使用的全局选项

logging（）服务日志选项

zone .（） DNS域的解析

#### 实战场景1  A地址解析

将www.imooc.com   A   

```
options{
	directory "/var/named"
};
zone "imooc.com" {
	type master;
	file "imooc.com.zone";
};
```



```
$TTL 7200
imooc.com. IN SOA imooc.com. jeson.imooc.com (222 1H 15M 1W 1D)
imooc.com. IN NS dns1.imooc.com.
dns1.imooc.com. IN A 116,196,95,138
www.imooc.com. IN A 2.2.2.2
```



dig  @116.196,95,138   www.imooc.com



####  重点总结

1. 严格注意语法书写，其格式非常严格
2. @是DNS记录中的保留至，表示当前域名
3. 记录不准许折行书写
4. 单行记录开头不准许空格或tab开头

#### 

#### 实战场景二 CNAME解析

```
$TTL 7200
iaskjob.com. IN SOA iaskjob.com. jeson.iaskjob.com (222 1H 15M 1W 1D)
iaskjob.com. IN NS dns1.iaskjob.com.
dns1.iaskjob.com. IN A 116,196,95,138
imooc.iaskjob.com. IN CNAME www.imooc.com
```

```
options{
	directory "/var/named"
};
zone "imooc.com" {
	type master;
	file "imooc.com.zone";
};
zone "iasdkjob.com" {
	type master;
	file "iasdkjob.com.zone";
};
```



#### 正向解析和反向解析

正向解析 通过域名查找IP      A记录使用

反向解析 通过IP查找对应域名    PTR记录  通常用在邮件服务器，ＭＸ解析



#### 实战场景三，正向解析和反向解析

```
$TTL 7200
iaskjob.com. IN SOA iaskjob.com. jeson.iaskjob.com (222 1H 15M 1W 1D)
iaskjob.com. IN NS dns1.iaskjob.com.
dns1.iaskjob.com. IN A 116,196,95,138
imooc.iaskjob.com. IN CNAME www.imooc.co
＠　ＩＮ　ＭＸ　１０　ｍａｘ
ｍａｉｌ　inＡ　 116,196,95,138
```



```
options{
	directory "/var/named"
};
zone "imooc.com" {
	type master;
	file "imooc.com.zone";
};
zone "iasdkjob.com" {
	type master;
	file "iasdkjob.com.zone";
};
zone "95,196,116。in-adr.arpa" {
	type master;
	file "116,196,95.zone";
};
```

dig -x  -x表示反向解析



#### 重点总结

1. 逆向解析域in-add.arpa的书写格式
2. 常用与邮件服务的域名解析
3. 配置文件权限需要named用户可读取



#### 客户端工具

nslookup   支持多平台，应用广泛      使用简单，易懂

dig    常见与linux系统    比较专业

host    较多     简单，明了

host   -t SOA  www.baidu.com

host  -t NS  baidu.com

host -t A  baidu.com

host  baidu.com



nslookup  www.imooc.com

nslookup 

  server 127

  www.imooc.com

server  127,0,0,1

set q=soa

baidu.com



dig @127.0.0.1 



dig -x @127.0.0.1 

dig -t a baidu.com





## Bind负载均衡

DNS递归迭代





DNS转发

DNS主从模式

DNS传输限制





## 只能DNS


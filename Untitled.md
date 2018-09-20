#### B2G权限开发

新建Controller类，在类上添加的mkbh下面表中添加一条记录,在后台权限管理中就可以对当前controller进行权限控制了

```sql
select * from rep_mkb_vip
```



#### 清空报销单和出差申请

按会员ID重置报销单和出差申请单 适用于10000系统 ,注意修改hyid   

```sql
-----删除出差申请单
select *
  from vip_ccsqb_jt
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
select *
  from vip_ccsqb_mxzb
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
select *
  from vip_ccsqb_ry
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
select *
  from vip_ccsqb_rc
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
select *
  from vip_ccsqb_xc
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
select *
  from vip_ccsqb_zs
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
select * from vip_ccsqb where khid = '180820095619926041';
select * from vip_bpm where hyid = '180820095619926041';
select * from vip_bpm_spr where hyid = '180820095619926041';

delete from vip_ccsqb_jt
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
delete from vip_ccsqb_mxzb
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
delete from vip_ccsqb_ry
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
delete from vip_ccsqb_rc
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
delete from vip_ccsqb_xc
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
delete from vip_ccsqb_zs
 where sqdh in
       (select SQDH from vip_ccsqb where khid = '180820095619926041');
delete from vip_ccsqb where khid = '180820095619926041';
delete from vip_bpm where hyid = '180820095619926041';
delete from vip_bpm_spr where hyid = '180820095619926041';



---删除报销单
select * from vip_bxspmx where hyid = '180820095619926041';
select * from vip_bxsq where hyid = '180820095619926041';
select *
  from vip_bxsqbt
 where bxdh in
       (select bxdh from vip_bxsq where hyid = '180820095619926041');
select *
  from vip_bxsqmx
 where bxdbh in
       (select bxdh from vip_bxsq where hyid = '180820095619926041');
select *
  from vip_bxsqmx_zb
 where bxmxid in
       (select id
          from vip_bxsqmx
         where bxdbh in
               (select bxdh from vip_bxsq where hyid = '180820095619926041'));

delete from vip_bxspmx where hyid = '180820095619926041';
delete from vip_bxsq where hyid = '180820095619926041';
delete from vip_bxsqbt
 where bxdh in
       (select bxdh from vip_bxsq where hyid = '180820095619926041');
delete from vip_bxsqmx
 where bxdbh in
       (select bxdh from vip_bxsq where hyid = '180820095619926041');
delete from vip_bxsqmx_zb
 where bxmxid in
       (select id
          from vip_bxsqmx
         where bxdbh in
               (select bxdh from vip_bxsq where hyid = '180820095619926041'));

```


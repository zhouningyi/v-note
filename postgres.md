# postgres 常用sql

### 安装

#### centos

```shell
sudo yum install https://download.postgresql.org/pub/repos/yum/9.5/redhat/rhel-6-x86_64/pgdg-centos95-9.5-3.noarch.r个pm

sudo yum install postgresql95-server postgresql95-contrib
```


#### mac: 

主路径: `/usr/local/var/postgres`

$PGDATA: ``

postgres.app安装
安装postgres.app后, ~/.bash_profile 里追加
`export PATH=$PATH:/Applications/Postgres.app/Contents/Versions/latest/bin`

brew安装
```shell
brew install postgres
brew install postgis
initdb /usr/local/var/postgres
#启动服务
 
#停止服务
pg_ctl -D /usr/local/var/postgres stop -s -m fast

createuser -d -a -P postgres
```



###  启动

`brew services start postgresql`

### 看常用信息

所有db占硬盘的大小:

```sql
select datname, pg_size_pretty(pg_database_size(datname)) from pg_database;   
```

某db占硬盘的大小:

```sql
select pg_size_pretty(pg_database_size('dbname'));
```

某table占硬盘的大小:

```sql
select pg_size_pretty(pg_table_size('tablename'));
```


目前访问情况:

```sql
select * from pg_stat_activity;
```

所有表的信息:
```sql
select * from information_schema.tables where table_type = 'BASE TABLE' and table_schema not in('information_schema', 'pg_catalog')
```

某一个表的字段的信息：

~~~sql
select * from information_schema.columns
where table_schema='public' and table_name='users';
~~~

### 存在性

判断schema是否存在
```sql
SELECT 1 FROM pg_namespace WHERE nspname = 'schema_name'
```

### 连接表

```shell
psql -U kupai -h rm-2zemyd8m8n226shs7o.pg.rds.aliyuncs.com -p 3432 -d spider
```

### 备份表(dump)
```shell
 pg_dump -U name -h host -p port  spider -f filepath
```

有时需要设置COLLATE
```sql
ALTER TABLE  house_lianjia_communities
ALTER COLUMN house_type type Character Varying( 255 ) COLLATE "pg_catalog"."default"
```

### 导入备份

```shell
 psql -d dbname -U username -f filepath
```

```
pg_restore -U xxx -d dbname < filepath
```

### 表 -> 远端服务器
```shell
psql -U username -h hostname -d desintationdb -p port -f dumpfilename.sql
```
### 关闭连接
`brew services stop postgresql`
`pg_ctl -D /usr/local/var/postgres stop -s -m fast`

### 创建表 / 索引

创建表: 

```sql
drop table if exists track_kuaidi; --可以判断是否存在
CREATE TABLE track_kuaidi (
	driver_id TEXT,
    lat FLOAT,
    lng FLOAT,
    time TIMESTAMP
);
```

非常方便的create table，不必写schema
```sql
create TABLE track_kuaidi_test1 as (select a, b, c from tb);
```

如果忘了几何字段，可以补上: 

```sql
create index pt_index on track_kuaidi_test using gist (geom);
```



### 安装扩展
easy_install 安装pgxn 参考 [PostgreSQL Extensions on Mac OS X](http://www.reades.com/2015/12/11/installing-postgresql-extensions-on-mac-os-x/)
```shell
sudo easy_install pgxnclient
```

安装插件
```shell
sudo pgxn install 插件名
```

为某个db加入插件
```shell
pgxn load -d 数据库名 -U 用户名 -p 端口 插件名
```



### jsonb extend问题

9.5以下安装插件jsonbx, 更多参考 [德哥大作](https://yq.aliyun.com/articles/54646):

```sql
select jsonb_concat('{"a":1, "b":2}'::jsonb,'{"b":4, "c":3}'::jsonb)
```

### 导入导出

处理csv 可以考虑AWK。[例子](http://qinghua.github.io/awk/)

如果是本地数据到服务器的话，使用\copy
```sql
COPY dbname(field1, field2, field3) from './xxx.csv' with DELIMITER ',' CSV HEADER;
```

导出时候 可选择写个sql进行筛选
```sql
COPY (select * from xxx) TO './xxx.csv'
```

表导表，可以考虑: 

```sql

```



### 行列转换（可排序）

```sql
select array_agg(x) x,array_agg(y order by y desc) y from tb
```

### 数组拍平成行
```sql
SELECT unnest(array[1,3]); 
```

### 导入轨迹数据(纯点)

从csv导入轨迹数据(lat, lng, time)后，建立点几何对象:

```sql  
ALTER table track_kuaidi add COLUMN  pt geometry(POINT, 4326); 
```

表track_kuaidi很大，可建立小规模测试表

```sql
select * into track_kuaidi_test from track_kuaidi limit 10000;
```

我们可以这样设计轨迹表 ```track_kuaidi_track``` ：

```sql
insert into track_kuaidi_track 
SELECT
  driver_id,
  ST_MakeLine(pt ORDER BY time ASC) AS geom
FROM track_kuaidi_test
GROUP BY driver_id;
```

但轨迹最好带有时间参数，因此这么做更好，[参考这里](http://permalink.gmane.org/gmane.comp.gis.postgis/37468) :
```sql
insert into track_kuaidi_track 
SELECT
  driver_id,
  ST_MakeLine(
    ST_MakePointM(ST_X(pt), ST_Y(pt), extract(epoch from time)::integer) ORDER BY time ASC
  ) AS geom
FROM track_kuaidi_test
GROUP BY driver_id;
```

其中
```sql
extract(epoch from time)::integer
```
是把 ```TIMESTAMP``` 转化为1970年开始的秒数，```ST_MakePointM 只能接受float型的参数。



另外，这个表的建立是

```sql
drop table if exists track_kuaidi_track;
create table track_kuaidi_track(
  driver_id text,
  geom geometry(LINESTRINGM, 4326)
);
```

注意 geom字段的 ```geometry(LINESTRINGM, 4326)```



最后建立几何索引 不再详述。

### upsert
```sql
INSERT INTO b (pk_b, b, comment) 
SELECT pk_a, a, comment
FROM   a 
ON     CONFLICT (pk_b) DO UPDATE  -- conflict is on the unique column
SET    b = excluded.b;  --注意这里是excluded
```


### 查询

1、 轨迹与geojson格式的包围圈有交集;
```sql
with geojson as (
 select st_geomFromGeojson(
  '{"type":"LineString","coordinates":[[120.19085884094238,30.26358944099115],[120.19085884094238,30.275524183056834],[120.20055770874025,30.277154904692335],[120.20047187805174,30.271373132994828],[120.20124435424806,30.262625702860728],[120.19103050231934,30.262996372491795],[120.19085884094238,30.26358944099115]]}'
 )
)
```
select 
where st_intersects()



2、随机抽取n条

```sql
SELECT * FROM tbs 
ORDER BY random() 
LIMIT n;
```

3、查询结果合并 UNION
```sql
SELECT field_1[, field_2,…]
FROM table_1[, table_2,…]
UNION [ALL]
SELECT field_a[, field_b,...]
FROM table_a[, table_b,…];
```

4、 '' 引号转义

### 分词/文本搜索
把句子拆分成分词后拍平
```sql
	UNNEST( regexp_split_to_array(regexp_replace(to_tsvector('jiebacfg',xxxx)::text,'(:\d+)', '', 'g'), ' ')
	) as yyyyy
```

中文分词，可以选择zhparser, pg_jieba

词云统计
```sql
SELECT * FROM ts_stat('SELECT content_tvector FROM dncs WHERE "from" like ''%@dnc.org''')
    ORDER BY nentry DESC;
```


[PostgreSQL的全文检索系统之基本介绍](https://www.rails365.net/articles/postgresql-de-quan-wen-jian-suo-xi-tong-zhi-ji-ben-jie-shao-yi)

### postGIS

计算面积，距离等，一种通用性的做法是转化为 geography 对象
```sql
st_area(geom :: geography)
```

### 同步数据
[PostgreSQL使用 postgres_fdw 进行跨库操作](https://dreamer-yzy.github.io/2015/01/05/PostgreSQL%E4%BD%BF%E7%94%A8-postgres-fdw-%E8%BF%9B%E8%A1%8C%E8%B7%A8%E5%BA%93%E6%93%8D%E4%BD%9C/)

collate是个大坑 导致dump无法复制到服务器



爬虫: 2000小区 

接口:  小区数据dump

交通: 公交线经过



## 资料

[awesome-postgres EN](https://github.com/dhamaniasad/awesome-postgres#gui)

[postgres学习资料](https://github.com/ty4z2008/Qix/blob/master/pg.md)

[服务器部署postgres](https://mos.meituan.com/library/28/how-to-install-postgresql9/)

[Postgres的存储过程简介 CN](http://www.jasongj.com/2015/12/27/SQL4_%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B_Store%20Procedure/)


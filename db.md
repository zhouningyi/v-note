# hive

[hive入门](http://mp.weixin.qq.com/s?__biz=MzIzODExMDE5MA==&mid=2694182433&idx=1&sn=687b754cddc7255026434c683f487ac0#rd)

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
 pg_dump -U name -h host -p port spider -f filepath
```


### 导入备份
```shell
 psql -d dbname -U username -f filepath
```

### 表 -> 远端服务器
```shell
psql -U username -h hostname -d desintationdb -p port -f dumpfilename.sql
```

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

### 分词
把句子拆分成分词后拍平
```sql
	UNNEST( regexp_split_to_array(regexp_replace(to_tsvector('jiebacfg',xxxx)::text,'(:\d+)', '', 'g'), ' ')
	) as yyyyy
```


### postGIS

计算面积，距离等，一种通用性的做法是转化为 geography 对象
```sql
st_area(geom :: geography)
```




### 中文分词
中文分词，可以选择zhparser, pg_jieba





爬虫: 2000小区 

接口:  小区数据dump

交通: 公交线经过
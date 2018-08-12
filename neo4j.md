# 图数据库

元数据展示
```
CALL apoc.meta.graph()
```

apoc的帮助函数
```
call apoc.help('apoc.meta.graph')
```

连接不同数据库需要下载不同的插件，比如关系型数据库: jdbc


删除节点时候，删除附带的边
```
DETACH DELETE 
```
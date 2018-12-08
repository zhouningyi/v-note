# 常用工具


## CHROME插件
Octotree: 可以像文件夹一样看github



##sublime插件

[好用的sublime插件](http://www.jianshu.com/p/3cb5c6f2421c/)



fileheader: 可以给代码头部一键插入签名

HTML-CSS-JS Prettify: 美化代码


[一个非常好用的data pipeline管理工具 airflow](http://www.jianshu.com/p/59d69981658a)


## supervisor的技巧
如何传入参数
```shell 
  node index.js -n okex_full_tick --proxy --db_id local_bfs
```

=> 
```shell
  supervisor -- index.js -n okex_full_tick --proxy --db_id local_bfs
```

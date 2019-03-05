# nodejs


## 规范
[package.json规范](http://javascript.ruanyifeng.com/nodejs/packagejson.html)

## 命令
寻找node安装路径:
```shell
  which node
```

杀掉所有的node:
```shell
  killall node
```

刷新require的缓存
```javascript
  Object.keys(require.cache).forEach(function(key) { delete require.cache[key]; });
  const xxx = require('xxx');
```

## 发布包
```javascript
npm adduser 
```

## 子进程
[使用node子进程spawn,exec踩过的坑](http://div.io/topic/1516)


## 数据库相关
[Sequelize 中文API文档 CN](http://itbilu.com/nodejs/npm/V1PExztfb.html)

[graphql入门 CN](http://taobaofed.org/blog/2015/11/26/graphql-basics-server-implementation/)
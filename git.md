# github相关

1、同时同步到github与gitlab:
修改 .git/config

```
[remote "origin"]
	url = github地址
	url = gitlab地址
```



2、子模块

删除
```
git submodule deinit -f --all
git rm --cached 路径
```
添加

```
git submodule add -f git地址 路径
git submodule update --remote
```

下载 
```
  git clone xx.git --recursive
```
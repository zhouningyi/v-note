#Linux 学习




### mac命令 
显示隐藏文件
```shell
defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder
```


### 插件

terminal  美化[OhmyZsh](http://ohmyz.sh/)

让某个js可执行
```shell
chmod +x testsh.js
```

### 命令

记住服务器密码
```shell
brew install ssh-copy-id 
```


解压缩

```shell
  unzip FileName.zip
  tar xvf FileName.tar
```



显示大文件前10行

head -n 10 filename

并且输出文件

head -n 10 filename > filename2



### 小软件 

命令行代理 `brew install proxychains-ng`

修改配置 `vim /usr/local/etc/proxychains.conf`


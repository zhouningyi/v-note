# 爬虫


[知乎爬虫精华 CN](https://www.zhihu.com/topic/19577498)

[开源项目专做防爬虫](https://www.zhihu.com/question/26221432)

[开发网络爬虫应该怎样选择爬虫框架？](http://blog.csdn.net/ajaxhu/article/details/42122461)

[Python爬虫进阶一之爬虫框架概述](https://cuiqingcai.com/2433.html)

### 在线爬虫

[import.io](http://import.io)<br>

[scrapinghub](http://scrapinghub.com/portia/)<br>

### ip代理相关

[如何检测IP CN](http://devco.re/blog/2014/06/19/client-ip-detection/)

[如何获取代理 CN](http://kaito-kidd.com/2015/11/02/proxies-service/)

[秘密代理(列表) CN](http://www.mimiip.com/)

[ip84代理(列表) CN](http://www.ip84.com/all)

直接連線 （沒有使用 Proxy）

REMOTE_ADDR: 客戶端真實 IP
HTTP_VIA: 無
HTTP_X_FORWARDED_FOR: 無
Transparent Proxy

REMOTE_ADDR: 最後一個代理伺服器 IP
HTTP_VIA: 代理伺服器 IP
HTTP_X_FORWARDED_FOR: 客戶端真實 IP，後以逗點串接多個經過的代理伺服器 IP
Anonymous Proxy

REMOTE_ADDR: 最後一個代理伺服器 IP
HTTP_VIA: 代理伺服器 IP
HTTP_X_FORWARDED_FOR: 代理伺服器 IP，後以逗點串接多個經過的代理伺服器 IP
High Anonymity Proxy (Elite Proxy)

REMOTE_ADDR: 代理伺服器 IP
HTTP_VIA: 無
HTTP_X_FORWARDED_FOR: 無 (或以逗點串接多個經過的代理伺服器 IP)


### 爬虫技巧

[常见的反爬虫和应对方法 CN](http://zhuanlan.zhihu.com/python-hacker/20520370)

### 模拟浏览器
[phantomjs综述 CN](http://javascript.ruanyifeng.com/tool/phantomjs.html)

### 分布式爬虫
[cola框架的介绍](https://github.com/chineking/cola/wiki)
[SeimiCrawler的介绍](https://github.com/zhegexiaohuozi/SeimiCrawler)
[几种分布式爬虫框架](https://yq.aliyun.com/articles/37419)


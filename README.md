# 读写分离
## 采用数据库中间件Mycat来做中间层
* 对原有系统没有任何代码和配置的 ``入侵 ``，耦合度最低；
* 通过 ``SHOW SLAVE STATUS``提供心跳机制，保证主从数据库在down掉其中一台时，依旧保证高可用，这是项目给中配置多数据源较难完成的任务；
* 可以根据主从延时较高时读从从库切到主库，待延时恢复后再切换到从库读取数据;
*  `` 如果某些对实时要求很高的读业务，比如毫秒级的，可以强制读主，最简单的实现就是代码层加入事务  `` 。

## 测试环境
### mycat部署在192.168.0.107,目录/opt/mycat/，SSH账号/密码:root/123456,查看日志:    tail -f /opt/mycat_1.6/logs/mycat.log
### 对应的主库:192.168.0.25:3307/dotdotbuy ``从3307/dotdotbuy拷贝的，JDBC千万不要连这个库,不然使用不了mycat的读写分离 ``，账号/密码:writer/daigou@123Db
### 对应的从库:192.168.0.107:3307/dotdotbuy 账号/密码:writer/daigou@123Db,``JDBC千万不要连这个库,不然主从数据库就不同步了 ``
## JDBC配置

* ddb.XXX.jdbc.url=jdbc:mysql://192.168.0.107:8099/dotdotbuy?useUnicode=true&characterEncoding=utf-8
* ddb.XXX.jdbc.username=writer
* ddb.XXX.jdbc.password=daigou@123Db

## mycat高可用
* mycat本身不支持高可用，需要借助lvs或HAProxy来做代理保证高可用
* lvs或HAProxy本身需要虚拟IP keepalived，目前外网服务器不支持虚拟IP， ``待解决高可用问题 ``

# 分片(未完待续)

---
title: mongodb本地开启副本集
date: 2023-06-23 19:43:26
tags:
id: 1687520641
---
mongodb 使用事务需要开启集群。首先在配置 mongod.conf里加入
```
replication:
  replSetName: rs0
```

重启之后在客户端执行命令
```
rs.initiate()
```

客户端重新连接后就可以使用了

---------------------------
参考：
https://www.mongodb.com/docs/manual/tutorial/convert-standalone-to-replica-set/
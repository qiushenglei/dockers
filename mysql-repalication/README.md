## 问题
因为把镜像保存路径放到了D，导致了my.cnf挂载到ubuntu时权限变成了777，mysql服务启动时会报"my.cnf因为权限过大，忽略此文件"。所以需要给文件修改权限
```yml
# master启动
$PWD/mysql-master/conf/my.cnf:/etc/mysql/my.cnf:ro	#master容器使用":ro"可以修改成可读，但是slave服务不行

# slave 只能使用command命令，不知道为什么
command: >
  bash -c "
  chmod 644 /etc/mysql/*.cnf
  && /entrypoint.sh mysqld
  "

```

## slave配置
>mysql:5.6版本的mysql，my.cnf已经没有master_host配置，所以使用了启动slave后手动配置
```mysql
stop slave;

CHANGE MASTER TO master_host = "mysql-master",
master_user = "root",
master_port = 3306,
master_password = "123456",
master_log_file = "mysql-bin.000015",
master_log_pos = 0;

start slave;

show slave status;
```

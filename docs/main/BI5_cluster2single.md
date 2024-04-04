如果要把集群状态下的工程换回单机，或者拿了集群工程的`finedb`到本地使用，那么`finedb`中某些集群配置就需要改一下，尤其是通过集群下的备份`finedb`拿到本地单机运行的话，文件服务器、状态服务器这些都需要改。

拿到备份的`finedb`用`dbeaver`连接，修改`fine_conf_entity`表的以下配置：

| 配置项名称                                           | 值/作用                                                                                                            |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| FineClusterConfig.params.cluster                     | 集群状态，`true`：开启，`false`：关闭                                                                              |
| ResourceModuleConfig.repositoryName                  | 资源配置的仓库名值，连接 ftp/sftp 的话值固定为 DECISION_FTP/DECISION_SFTP，否则为 LOCAL_ENV <br>单机可直接删除该项 |
| ResourceModuleConfig.repositoryNameOnboot            | 为空                                                                                                               |
| ResourceModuleConfig.sharedRepository                | 为空                                                                                                               |
| StateServerConfig.clusterMode                        | 状态服务器是否开启：`true`：开启，`false`：关 <br>单机下改为 false                                                 |
| StateServerConfig.type                               | 状态服务器连接类型：redis                                                                                          |
| SystemOptimizationConfig.biClusterMasterNodeHostName |                                                                                                                    |
| SystemOptimizationConfig.ClientMasterId              |                                                                                                                    |
| hotBackConf.master                                   |                                                                                                                    |
| hotBackConf.slave                                    |                                                                                                                    |

可直接执行 sql：

```sql
update FINE_CONF_ENTITY SET value='false' WHERE id = 'FineClusterConfig.params.cluster';

DELETE FROM FINE_CONF_ENTITY WHERE id='ResourceModuleConfig.repositoryName';

DELETE FROM FINE_CONF_ENTITY WHERE id = 'ResourceModuleConfig.sharedRepository';

DELETE FROM FINE_CONF_ENTITY WHERE id = 'ResourceModuleConfig.repositoryNameOnboot';

update FINE_CONF_ENTITY SET value='false' WHERE id = 'StateServerConfig.clusterMode';

update FINE_CONF_ENTITY SET value='standalone' WHERE id = 'StateServerConfig.type';
```

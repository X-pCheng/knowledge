清楚 appflowy 这个[架构](https://docs.appflowy.io/docs/documentation/appflowy-cloud/deployment)很重要
![alt text](../_attch_file/appflowy-arc.png)
因为后面的 docker 部署里面的组件就是这样规划的，清楚后自己调整起来（减少组件）也方便

# Docker

本示例通过 Docker 部署，遇到也有挺多问题，主要是环境变量的 Docker compose 文件的变化，其他的操作照着官网说明一步步来就可以

最大的不同就是：pg 用的库是新建的 appflowy，不是默认的 postgres，遇到挺多问题

步骤主要是按照 [Github 部署文档](https://github.com/AppFlowy-IO/AppFlowy-Cloud/blob/main/doc/DEPLOYMENT.md)来跟着操作的

## Docker compose

我的 compose 文件只保留了必要的组件，其他的都是本机其他已经有的容器，我的compose文件[在这里](main/appflowy_compose_my.md)

## 设置环境变量

环境变量通过`.env`文件进行配置，修修改改反正官网没得用，自己就改了一版[环境变量文件](main/appflowy_env_my.md)

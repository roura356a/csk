# 验证Kubernetes NodePort服务

在Kubernetes-piggymetrics-cluster集群中，NodePort类型的Service用于从Swarm集群的SLB上引入生产流量。本文主要介绍如何验证Kubernetes NodePort服务。

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**服务与路由** \> **服务**。

5.  查看服务是否创建成功。

    在服务列表中，查看gateway-swarm-slb为NodePort。

    ![查看服务](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1383463161/p241384.png)

6.  验证服务是否正常。

    本例中，我们通过随机登录一台Kubernetes集群的ECS服务器，请求对应的NodePort服务，看能否正常访问服务；示例是30080端口，ECS机器IP为 192.168.XX.X0。

    1.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

    2.  在节点页面，选择目标集群Kubernetes-piggymetrics-cluster，单击目标实例。

        ![节点列表](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8525463161/p48071.png)

    3.  在实例基本信息页面，单击**远程连接**，根据界面提示登录该实例。

    4.  在192.168.XX.X0机器上，通过ping、telnet、wget等命令访问 192.168.XX.X1:30080，确认该NodePort Service是否正常。

        ![NodePort Service](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8525463161/p48072.png)

        由于容器服务针对每个集群ECS实例都会自动创建一个网络安全组，其默认只允许集群内容器所属网段IP访问各个ECS实例端口，集群外部无法访问（SLB不受影响）。因此，我们在上述NodePort Service测试时选择利用Kubernetes 集群内的ECS实例做测试，您可以通过如下操作，访问网络安全组。

        1.  登录[ECS管理控制台](https://ecs.console.aliyun.com/)，在右侧导航栏选择**网络和安全** \> **安全组**，在目标安全组右侧单击**配置规则**。

            ![配置规则](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/8525463161/p48073.png)

        2.  在**安全组规则**页面，在**入方向**页签 ，可以看到该网络安全组的IP及端口。

            ![安全组规则](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/1383463161/p48074.png)

    验证NodePort服务正常后，您需要修改Swarm集群所属SLB的监听规则，将我们的Kubernetes 集群的NodePort服务挂载到Swarm集群的SLB中。更多信息，请参见[修改Swarm SLB配置](/intl.zh-CN/最佳实践/Swarm迁移Kubernetes/SLB灰度引流/修改Swarm SLB配置.md)。



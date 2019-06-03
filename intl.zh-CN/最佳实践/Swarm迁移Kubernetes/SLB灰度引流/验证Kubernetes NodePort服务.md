# 验证Kubernetes NodePort服务 {#task_371766 .task}

1.  查看服务是否创建成功。 
    1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)，在左侧导航栏选择**路由与负载均衡** \> **服务**，在服务列表中，查看gateway-swarm-slb为NodePort。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301865/155954565648070_zh-CN.png)

2.  验证服务是否正常。 本例中，我们通过随机登录一台Kubernetes 集群的ECS服务器，请求对应的NodePort服务，看能否正常访问服务；示例是30080端口，ECS机器IP为 192.168.0.250。

    1.  在左侧导航栏选择**集群** \> **节点**，在节点列表页面，选择目标集群Kubernetes-piggymetrics-cluster，单击目标实例。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301865/155954565648071_zh-CN.png)

    2.  在实例基本信息页面，单击**远程连接**，根据界面提示登录该实例。
    3.  在192.168.0.250机器上，通过ping、telnet、wget等命令访问 192.168.0.251:30080；确认该NodePort Service是否正常。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301865/155954565648072_zh-CN.png)

        由于容器服务针对每个集群ECS实例都会自动创建一个网络安全组，其默认只允许集群内容器所属网段IP访问各个ECS实例端口，集群外部无法访问（SLB不受影响）；因此，我们在上述NodePort Service测试时选择利用Kubernetes 集群内的ECS实例做测试，您可以通过如下操作，访问网络安全组。

        1.  登录[ESC管理控制台](https://ecs.console.aliyun.com/)，在右侧导航栏选择**网络和安全** \> **安全组**，在目标安全组右侧单击**配置规则**。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301865/155954565648073_zh-CN.png)

        2.  在**安全组规则**页面，在**入方向**页签 ，可以看到该网络安全组的IP及端口。

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/301865/155954565648074_zh-CN.png)

    验证NodePort服务正常后，您需要通过[修改Swarm SLB配置](intl.zh-CN/最佳实践/Swarm迁移Kubernetes/SLB灰度引流/修改Swarm SLB配置.md#)，修改Swarm集群所属SLB的监听规则，将我们的Kubernetes 集群的NodePort服务挂载到Swarm集群的SLB中。



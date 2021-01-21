# Service的负载均衡配置注意事项

当Service的类型设置为`Type=LoadBalancer`时，容器服务ACK的CCM（Cloud Controller Manager）组件会为该Service创建或配置阿里云负载均衡SLB（Server Load Balancer），包括含SLB、监听、后端服务器组等资源。本文介绍配置Service负载均衡的注意事项以及CCM的资源更新策略。

## SLB更新策略

ACK支持为Serivce指定一个已有的SLB，或者让CCM自动创建新的SLB。两种方式在SLB的资源更新策略方面存在一些差异，如下表所示。

|资源对象|指定已有SLB|CCM管理SLB|
|----|-------|--------|
|SLB|设置annotation：`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-id`。-   CCM会使用该SLB作为Service的负载均衡，并根据其他annotation配置SLB，自动为SLB创建多个虚拟服务器组。
-   当Service删除时，CCM不会删除您通过ID指定的已有SLB。

|-   CCM会根据Service的配置，自动创建和配置SLB、监听、虚拟服务器组等资源，所有资源由CCM管理。
-   当Service删除时，CCM会删除自动创建的SLB。 |
|监听|设置annotation：`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners:`：-   如果设置为false，CCM不会为SLB管理任何监听配置。
-   如果设置为true，CCM会根据Service配置管理监听；如果监听已经存在，则CCM会覆盖已有监听。

|CCM会根据Service的配置，自动创建和配置监听策略。|
|后端服务器组|当Service对应的后端Endpoint或者集群节点发生变化时，CCM会自动更新SLB的后端虚拟服务器组。-   根据Service模式的不同，后端服务器组的更新策略也有所不同。
    -   Cluster模式（`spec.externalTrafficPolicy = Cluster`）：CCM默认会将所有节点挂载到SLB的后端（使用BackendLabel标签配置后端的除外）。

**说明：** SLB存在配额限制，限制了每个ECS能够使用的SLB个数，这种方式会快速消耗该配额。当配额耗尽后，会造成Service Reconcile失败。解决办法：使用Local模式的Service。

    -   Local模式（`spec.externalTrafficPolicy = Local`）：CCM默认只会将Service对应的Pod所在节点加入到SLB后端。这样可以降低SLB配额的消耗速度，同时支持四层源IP保留。
-   任何情况下CCM都不会将Master节点作为SLB的后端。
-   CCM默认不会从SLB后端移除被驱逐（`kubectl drain`）或停止调度（`kubectl cordon`）的节点。如需移除节点，请设置`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend`为`on`。

**说明：** 如果是v1.9.3.164-g2105d2e-aliyun之前的版本，CCM默认会从SLB后端移除被驱逐或停止调度的节点。 |

## 指定已有SLB时注意事项

-   **哪些SLB可以被复用？**
    -   仅支持复用通过SLB控制台创建的SLB，不支持复用CCM自动创建的SLB。
    -   如果您需要在Kubernetes集群中复用私网类型的SLB，则该SLB需要和Kubernetes集群处于同一VPC下。
-   CCM只为`Type=LoadBalancer`类型的Service配置SLB，对于非LoadBalancer类型的Service则不会为其配置负载均衡。

    **说明：** 当`Type=LoadBalancer`的Service变更为`Type!=LoadBalancer`时，CCM会删除为该SLB添加的配置，从而造成无法通过该SLB访问Service。

-   CCM使用声明式API，会在一定条件下自动根据Service的配置刷新SLB配置。当`service.beta.kubernetes.io/alibaba-cloud-loadbalancer-force-override-listeners:`设置为`true`时，您自行在SLB控制台上修改的配置均存在被覆盖的风险。

    **说明：** 请勿在SLB控制台上手动修改Kubernetes创建并维护的SLB的任何配置，否则有配置丢失的风险，造成Service不可访问。


## CCM管理SLB时注意事项

-   CCM只为`Type=LoadBalancer`类型的Service创建SLB，对于非LoadBalancer类型的Service则不会为其创建或配置负载均衡。

    **说明：** 当`Type=LoadBalancer`的Service变更为`Type!=LoadBalancer`时，CCM也会删除其原先为该Service创建的SLB。

-   CCM使用声明式API，会在一定条件下自动根据Service的配置更新SLB配置。您自行在SLB控制台上修改的配置均存在被覆盖的风险。

    **说明：** 请勿在SLB控制台上手动修改Kubernetes创建并维护的SLB的任何配置，否则有配置丢失的风险，造成Service不可访问。


## 配额限制

**VPC**

-   集群中一个节点对应一条路由表项，VPC默认情况下仅支持48条路由表项，如果集群节点数目多于48个，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)给VPC产品。

    **说明：** 您可以在提交工单时，说明需要修改`vpc_quota_route_entrys_num`参数，用于提升单个路由表可创建的自定义路由条目的数量。

-   更多VPC使用限制，请参见[使用限制](/cn.zh-CN/产品简介/使用限制.md)。

    查询VPC配额，请参见[专有网络VPC配额管理](https://vpc.console.aliyun.com/quota%EF%BC%89)。


**SLB**

-   CCM会为`Type=LoadBalancer`类型的Service创建SLB。默认情况下一个用户可以保留60个SLB实例。如果需要创建的SLB数量大于60，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)给SLB产品。

    **说明：** 您可以在提交工单时，说明需要修改`slb_quota_instances_num`参数，用于提高您可保有的SLB实例个数。

-   CCM会根据Service的配置将ECS挂载到SLB后端服务器组中。
    -   默认情况下一个ECS实例可挂载的后端服务器组的数量为50个，如果一台ECS需要挂载到更多的后端服务器组中，请[提交工单](https://selfservice.console.aliyun.com/ticket/createIndex)给SLB产品。

        **说明：** 您可以在提交工单时，说明需要修改`slb_quota_backendserver_attached_num`参数，用于提高同一台服务器可以重复添加为SLB后端服务器的次数。

    -   默认情况下一个SLB实例可以挂载200个后端服务器，如果需要挂载更多的后端服务器，请提交工单给SLB产品。

        **说明：** 您可以在提交工单时，说明需要修改`slb_quota_backendservers_num`参数，提高每个SLB实例可以挂载的服务器数量。

-   CCM会根据Service中定义的端口创建SLB监听。默认情况下一个SLB实例可以添加50个监听，如需添加更多监听，请提交工单给SLB产品。

    **说明：** 您可以在提交工单时，说明需要修改`slb_quota_listeners_num`参数，用于提高每个实例可以保有的监听数量。

-   更多SLB使用限制请参见[使用限制](/cn.zh-CN/传统型负载均衡CLB/用户指南/产品限制/使用限制.md)。

    负载均衡SLB配额查询请参见[负载均衡SLB配额管理](https://slbnew.console.aliyun.com/slb/quota)。



# 变更记录-Cloud Controller Manager {#concept_wk1_grd_qfb .concept}

本文为您介绍了Cloud Controller Manager相关内容的最新动态。

|变更时间|镜像地址|变更内容|
|----|----|----|
|2019.04.15|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.105-gfd4e547-aliyun| -   支持VPC多路由表。允许通过配置文件的方式为集群配置多个路由表。
-   修复HTTP协议配置更新不生效的问题。

 |
|2019.03.20|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.81-gca19cd4-aliyun| -   Managed Kubernetes及Dedicated Kubernetes 支持复用已有非Kubernetes创建的SLB。
-   CCM支持用户自定义Kubernetes节点名称。不再强依赖Kubernetes NodeName。
-   修复CCM 1.8.4版本与Kubernetes1.11.5的兼容性问题。请升级CCM到最新版本。

 |
|2018.12.26|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.59-ge3bc999-aliyun| 支持多个Kubernetes Service复用同一个SLB。

-   Kubernetes通过Service创建的SLB不能复用（会导致SLB被意外删除）。只能复用您手动在控制台（或调用OpenAPI）创建的SLB。
-   复用同一个SLB的多个Service不能有相同的前端监听端口，否则会造成端口冲突。
-   复用SLB时，请使用监听的名字和虚拟服务器组的名字作为标识符。请勿修改监听和虚拟服务器组的名字。
-   SLB的名字可以修改。
-   不支持跨集群复用SLB。

 操作VPC路由表方式由并行改为串行方式，修复了触发VPC限流问题。

 |
|2018.8.15|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun| -   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid指定自动创建的SLB所处的主可用区。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid指定自动创建的SLB所处的备可用区。

**说明：** 某些Region不支持创建主备可用区类型的SLB，该参数不起作用。

-   在指定已有SLB时，支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners覆盖式处理原有SLB上的监听。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth为创建的按带宽付费的SLB指定带宽值。其中多个Listener共享该带宽。

 |
|2018.6.25|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3| -   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-backend-label让用户能够使用指定label的worker节点作为后端服务器。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-spec 指定SLB的类型，如性能共享型还是独占型。
-   支持service的externalTrafic: Local 模式。仅添加Pod所在的节点作为SLB的后端。
-   当集群节点有添加或者删除的时候，自动处理SLB的后端，同步添加移除相应节点。
-   当节点的label发生变化时，自动的处理SLB的后端，同步添加或移除相应的节点。
-   支持Session Sticky。
-   通过指定已有SLB创建的Service不再处理监听，需要用户自行添加SLB监听。

 |


---
keyword: Cloud Controller Manager
---

# Cloud Controller Manager

本文为您介绍了CCM（Cloud Controller Manager）组件信息、使用说明和变更记录。

## 组件介绍

Cloud Controller Manager提供Kubernetes与阿里云基础产品的对接能力，例如CLB（原SLB）、VPC等。目前CCM的功能包括管理负载均衡、跨节点通信等：

-   管理负载均衡

    当Service的类型设置为`Type=LoadBalancer`时，CCM组件会为该Service创建或配置阿里云负载均衡CLB，包括含CLB、监听、后端服务器组等资源。当Service对应的后端Endpoint或者集群节点发生变化时，CCM会自动更新CLB的后端虚拟服务器组。

-   实现跨节点通信

    当集群网络组件为Flannel时，CCM组件负责打通容器与节点间网络，实现容器跨节点通信。CCM会将节点的Pod网段信息写入VPC的路由表中，从而实现跨节点的容器通信。该功能无需配置，安装即可使用。


## 使用说明

-   CCM组件会为Service创建或配置阿里云负载均衡CLB，包括含CLB、监听、后端服务器组等资源。更多信息，请参见[Service的负载均衡配置注意事项](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service的负载均衡配置注意事项.md)。
-   CCM组件提供了许多注解（Annotation），支持云上丰富的负载均衡能力。详细的使用方式，请参见[通过Annotation配置负载均衡](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/通过Annotation配置负载均衡.md)。

## 变更记录

**2021年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v2.0.1|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v2.0.1|2021年09月02日|-   新功能：
    -   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vgroup-port复用已有虚拟服务器组。该Annotation仅在复用已有SLB场景下生效。
    -   在多个Service复用同一个SLB的场景下，支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-weight设置当前Service接收流量的权重。该Annotation仅在复用已有虚拟服务器组场景下生效。
    -   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-connection-drain管理SLB优雅中断，仅支持TCP和UDP协议。
    -   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-connection-drain-timeout设置SLB优雅中断超时时间，仅支持TCP和UDP协议。
    -   支持String类型的TargetPort。
    -   为LoadBalancer类型Service添加Finalizer。
-   改进：
    -   升级基础镜像版本到Alpine 3.13。
    -   Prometheus metrics端口从10258改为8080。
    -   定时同步节点标签。

|此次升级不会对业务造成影响。|

**2021年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.380-gd6d0962-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.380-gd6d0962-aliyun|2021年04月20日|-   修复默认服务器组无法更新的问题。
-   当SLB后端为空时，透出告警事件（Event）。

|此次升级不会对业务造成影响。|

**2021年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.378-g42eac35-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.378-g42eac35-aliyun|2021年03月08日|新功能：

-   支持为虚拟服务器组添加集群外ECS。
-   复用已有SLB时，默认为该SLB添加`kubernetes.reused.by.user`的标签。

优化：

-   调整Service并发处理线程数量，优化Service处理速度。
-   优化virtual-kubelet节点处理逻辑，忽略由virtual-kubelet节点状态变化引发的Service同步。
-   排除节点的标签`service.beta.kubernetes.io/exclude-node`已被废弃，请使用新标签`service.alibabacloud.com/exclude-node`代替。
-   复用已有SLB时增加资源组校验，注解中的资源组ID需要与该SLB的资源组ID一致，否则复用失败。
-   优化事件（Events）内容，提升可读性。
-   优化新版本与老版本注解的优先级配置，如果Service上同时存在新版本和老版本的同名注解时，新版本注解优先生效。

缺陷修复：

-   修复因节点配置缺失导致的路由删除失败问题。
-   优化节点初始化逻辑，修复污点（Taint）缺失问题。在节点初始化过程中，避免业务Pod调度到路由未创建的节点上。

|此次升级不会对业务造成影响。|

**2020年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.339-g9830b58-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.339-g9830b58-aliyun|2020年12月18日|-   支持为LoadBalancer类型Service添加Hash值，以保证CCM重启时在Service未做修改的情况下，仅同步虚拟服务器组后端，不再同步LoadBalancer配置及监听配置。
-   优化SLB OpenAPI调用，降低被限流风险。

|此次升级不会对业务造成影响。|

**2020年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.316-g8daf1a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.316-g8daf1a9-aliyun|2020年09月29日|-   修复偶发性SLB虚拟服务器组未更新问题。
-   更新健康检查端口（从10252变更为10258）。

|此次升级不会对业务造成影响。|

**2020年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.313-g748f81e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.313-g748f81e-aliyun|2020年08月10日|-   新功能：

-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection设置SLB删除保护，新建SLB默认开启删除保护。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection设置SLB配置修改保护，新建SLB默认开启配置修改保护。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id指定SLB所属的资源组，仅在创建时生效，不支持修改。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name指定SLB名称。
-   阿里云产品OpenAPI调用方式从公网改为内网，去除CCM的公网依赖（已支持全部地区）。
-   对于LoadBalancer类型Service创建的SLB默认添加Tag，其格式为`ack.aliyun.com: {your-cluster-id}`（仅对新建集群生效）。
-   兼容社区provider ID命名方式`<cloudProvider>://<optional>/<segments>/<provider id>`。
-   新建Terway集群的LoadBalancer类型的Service，默认将Pod直接挂载到SLB后端。对于新建Terway网络模式的ACK集群，如果Service类型是LoadBalancer，则默认直接挂载Pod的ENI IP做为负载均衡的后端，提升网络性能（对于LoadBalancer类型的Service，暂不支持string类型的targetPort）。
-   改进：

-   升级基础镜像版本到Alpine 3.11.6。
-   更新监听，将会同步更新虚拟服务器组。
-   优化SLB API，减少SLB创建时间。

|此次升级不会对业务造成影响。|

**2020年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.276-g372aa98-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.276-g372aa98-aliyun|2020年06月11日|-   新功能：

-   对于LoadBalancer类型的Service，限制复用集群API Server的SLB。
-   新增Prometheus Metrics（ccm\_node\_latencies\_duration\_milliseconds、ccm\_route\_latencies\_duration\_milliseconds、ccm\_slb\_latencies\_duration\_milliseconds），用于透出CCM同步时延信息。
-   支持以Event方式透出Service与LoadBalancer同步过程。
-   改进：
    -   优化Local模式下（设定Service的externalTrafficPolicy=Local）权重计算方式，使Pod间负载更加均衡，详情请参见[Local模式下如何自动设置Node权重？](/cn.zh-CN/Kubernetes集群用户指南/网络/Service管理/Service FAQ.md)。

    -   优化云产品API调用，提升效率、降低限流风险。
    -   当节点有service.beta.kubernetes.io/exclude-node标签时，删除节点时不再删除关联路由。
-   修复缺陷：
    -   修复更新Service时，无法通过annotation设置persistence timeout为0的问题。
    -   修复更新Service时，无法通过annotation设置bandwidth为100的问题。

|此次升级不会对业务造成影响。|

**2020年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.239-g40d97e1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.239-g40d97e1-aliyun|2020年03月05日|-   新功能：

对于Loadbalancer类型的Service，CCM支持为SLB后端同时挂载ECS节点和弹性网卡ENI。

-   改进：
    -   阿里云产品OpenAPI调用方式从公网改为内网，去除CCM的公网依赖（北京、上海、迪拜暂不支持）。

    -   更换VPC路由查询接口为DescribeRouteEntryList，避免短时间内查询数百量级条目时存在的性能问题。


|此次升级不会对业务造成影响。|

**2019年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.220-g24b1885-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.220-g24b1885-aliyun|2019年12月31日|-   配置VSwitchids。CloudConfig中支持添加`:vswithid1,:vswitchid2`格式。
-   OpenAPI限流情况下，重试时添加Backoff机制，间隔30s-180s重新加回Reconcile队列。
-   调整Reconcile的Worker线程数目为2个，最大化的使用OpenAPI QPS配额，提升Reconcile的速度。
-   修复由于aliyungo SDK并发读写Map导致CCM崩溃的问题。
-   当节点从Kubernetes集群中移除时，CCM会自动清理该节点对应的VPC路由表条目。
-   修复Http Forward由于端口转发依赖而无法变更端口配置的问题。
-   如果SLB后端的类型为ECS，则更新SLB后端服务器时无需判断serverip字段，避免OpenAPI的serverip字段默认值变化引起的后端添加失败。
-   当节点状态为已知时，才会添加该节点对应的VPC路由表条目。
-   CCM不再为节点元数据添加Nat IP，修复了API Server到kubelet偶发性访问不通的问题。
-   变更监听配置时，仅在监听状态为inactive时调用start listener OpenAPI，避免引起OpenAPI限流问题。

|此次升级不会对业务造成影响。|

**2019年11月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.193-g6cddde4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.193-g6cddde4-aliyun|2019年11月19日|-   支持为节点添加label：service.beta.kubernetes.io/exclude-node，使得CCM不再管理该节点。
-   支持为SLB后端批量添加网络类型为Terway的Pod。
-   限制Local模式下（即设定service的externalTrafficPolicy=Local）Node权重不小于1。
-   修复因并发导致的重复创建虚拟服务器组的问题。
-   修复因缓存导致的设置Node权重时产生脏数据的问题。

|此次升级不会对业务造成影响。|

**2019年09月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.164-g2105d2e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3-164-g2105d2e-aliyun|2019年09月11日|-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id更新证书。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port实现http到https的端口转发。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status、service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id和service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type创建带有ACL的SLB。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend设定是否移除不可调度的节点。
-   支持在Terway网络模式下，通过annotation：service.beta.kubernetes.io/backend-type："eni"将Pod直接挂载到SLB后端，提升网络转发性能。
-   支持Local模式下（即设定service的externalTrafficPolicy=Local），Service自动根据Node上的Pod数量为Node设置权重。

|此次升级不会对业务造成影响。|

**2019年04月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.105-gfd4e547-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.105-gfd4e547-aliyun|2019年04月15日|-   支持VPC多路由表。允许通过配置文件的方式为集群配置多个路由表。
-   修复HTTP协议配置更新不生效的问题。

|此次升级不会对业务造成影响。|

**2019年03月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.81-gca19cd4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.81-gca19cd4-aliyun|2019年03月20日|-   Managed Kubernetes及Dedicated Kubernetes支持复用已有非Kubernetes创建的SLB。
-   CCM支持用户自定义Kubernetes节点名称。不再强依赖Kubernetes NodeName。
-   修复CCM 1.8.4版本与Kubernetes 1.11.5版本的兼容性问题。请升级CCM到最新版本。

|此次升级不会对业务造成影响。|

**2018年12月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.59-ge3bc999-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.59-ge3bc999-aliyun|2018年12月26日|-   支持多个Kubernetes Service复用同一个SLB。
    -   Kubernetes通过Service创建的SLB不能复用（会导致SLB被意外删除）。只能复用您手动在控制台（或调用OpenAPI）创建的SLB。
    -   复用同一个SLB的多个Service不能有相同的前端监听端口，否则会造成端口冲突。
    -   复用SLB时，请使用监听的名字和虚拟服务器组的名字作为标识符。请勿修改监听和虚拟服务器组的名字。
    -   SLB的名字可以修改。
    -   不支持跨集群复用SLB。
-   操作VPC路由表方式由并行改为串行方式，修复了触发VPC限流问题。

|此次升级不会对业务造成影响。|

**2018年08月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3.10-gfb99107-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun|2018年08月15日|-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid指定自动创建的SLB所处的主可用区。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid指定自动创建的SLB所处的备可用区。

**说明：** 某些Region不支持创建主备可用区类型的SLB，该参数不起作用。

-   在指定已有SLB时，支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners覆盖式处理原有SLB上的监听。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth为创建的按带宽付费的SLB指定带宽值。其中多个Listener共享该带宽。

|此次升级不会对业务造成影响。|

**2018年06月**

|版本号|镜像地址|变更时间|变更内容|变更影响|
|---|----|----|----|----|
|v1.9.3|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3|2018年06月25日|-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-backend-label让用户能够使用指定label的worker节点作为后端服务器。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-spec指定SLB的类型，如性能共享型还是独占型。
-   支持service的`externalTrafic: Local`模式。仅添加Pod所在的节点作为SLB的后端。
-   当集群节点有添加或者删除的时候，自动处理SLB的后端，同步添加移除相应节点。
-   当节点的label发生变化时，自动的处理SLB的后端，同步添加或移除相应的节点。
-   支持Session Sticky。
-   通过指定已有SLB创建的Service不再处理监听，需要用户自行添加SLB监听。

|此次升级不会对业务造成影响。|


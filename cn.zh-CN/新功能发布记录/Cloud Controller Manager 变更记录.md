# Cloud Controller Manager 变更记录 {#concept_wk1_grd_qfb .concept}

本文为您介绍了Cloud Controller Manager相关内容的最新动态。

## 2019年9月 {#section_s61_k8f_y3j .section}

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.9.3.164-g2105d2e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3-164-g2105d2e-aliyun|2019年9月11日| -   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id 更新证书。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port 实现http到https的端口转发。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status、service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id和service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type 创建带有ACL的SLB。
-   支持通过annotation：service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend 设定是否移除不可调度的节点。
-   支持在Terway 网络模式下，通过annotation：service.beta.kubernetes.io/backend-type："eni" 将Pod直接挂载到SLB后端，提升网络转发性能。
-   支持Local模式下（即设定service的externalTrafficPolicy=Local），Service自动根据Node上的Pod数量为Node设置权重。

 |

## 2019年4月 {#section_0yg_wr5_atj .section}

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.9.3.105-gfd4e547-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.105-gfd4e547-aliyun|2019年4月15日| -   支持VPC多路由表。允许通过配置文件的方式为集群配置多个路由表。
-   修复HTTP协议配置更新不生效的问题。

 |

## 2019年3月 {#section_7tn_2ki_55w .section}

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.9.3.81-gca19cd4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.81-gca19cd4-aliyun|2019年3月20日| -   Managed Kubernetes及Dedicated Kubernetes 支持复用已有非Kubernetes创建的SLB。
-   CCM支持用户自定义Kubernetes节点名称。不再强依赖Kubernetes NodeName。
-   修复CCM 1.8.4版本与Kubernetes1.11.5的兼容性问题。请升级CCM到最新版本。

 |

## 2018年12月 {#section_vkf_kah_a69 .section}

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.9.3.59-ge3bc999-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.59-ge3bc999-aliyun|2018年12月26日| -   支持多个Kubernetes Service复用同一个SLB。
    -   Kubernetes通过Service创建的SLB不能复用（会导致SLB被意外删除）。只能复用您手动在控制台（或调用OpenAPI）创建的SLB。
    -   复用同一个SLB的多个Service不能有相同的前端监听端口，否则会造成端口冲突。
    -   复用SLB时，请使用监听的名字和虚拟服务器组的名字作为标识符。请勿修改监听和虚拟服务器组的名字。
    -   SLB的名字可以修改。
    -   不支持跨集群复用SLB。
-   操作VPC路由表方式由并行改为串行方式，修复了触发VPC限流问题。

 |

## 2018年8月 {#section_nuh_gm9_zcq .section}

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.9.3.10-gfb99107-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun|2018年8月15日| -   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid指定自动创建的SLB所处的主可用区。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid指定自动创建的SLB所处的备可用区。

**说明：** 某些Region不支持创建主备可用区类型的SLB，该参数不起作用。

-   在指定已有SLB时，支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners覆盖式处理原有SLB上的监听。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth为创建的按带宽付费的SLB指定带宽值。其中多个Listener共享该带宽。

 |

## 2018年6月 {#section_vx7_gs5_jea .section}

|版本号|镜像地址|变更时间|变更内容|
|---|----|----|----|
|v1.9.3|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3|2018年6月25日| -   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-backend-label让用户能够使用指定label的worker节点作为后端服务器。
-   支持通过annotation：service.beta.kubernetes.io/alicloud-loadbalancer-spec 指定SLB的类型，如性能共享型还是独占型。
-   支持service的`externalTrafic: Local`模式。仅添加Pod所在的节点作为SLB的后端。
-   当集群节点有添加或者删除的时候，自动处理SLB的后端，同步添加移除相应节点。
-   当节点的label发生变化时，自动的处理SLB的后端，同步添加或移除相应的节点。
-   支持Session Sticky。
-   通过指定已有SLB创建的Service不再处理监听，需要用户自行添加SLB监听。

 |


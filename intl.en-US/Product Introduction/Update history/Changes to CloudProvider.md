# Changes to CloudProvider {#concept_wk1_grd_qfb .concept}

This topic describes the latest changes to CloudProvider.

|Date|Image address|Change|Relevant topic|
|----|-------------|------|--------------|
|August 15, 2018|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun| -   You can use annotation:service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid to specify the active zone in which an automatically created SLB resides.
-   You can use annotation:service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid to specify the standby zone in which an automatically created SLB resides.

**Note:** This parameter does not take effect in regions where an active/standby-zone SLB cannot be created.

-   When you specify an existing SLB, you can use annotation:service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners to override the listeners of the original SLB.
-   You can use annotation：service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth to set a bandwidth value for the created SLB that is billed by bandwidth. Multiple listeners share this bandwidth.

 |[Access services by using Server Load Balancer](../../../../reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress/Access services by using Server Load Balancer.md#)|
|2018.6.25|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3| -   You can use annotation:service.beta.kubernetes.io/alicloud-loadbalancer-backend-label to use the Worker node that has the specified label as the backend server.
-   You can use annotation：service.beta.kubernetes.io/alicloud-loadbalancer-spec to set an SLB type. For example, you can set a shared-performance SLB or an exclusive SLB.
-   The service mode externalTrafic: Local is available. Only the node in which a pod resides can be added as the SLB backend server.
-   When you add or remove a cluster node, the system automatically processes the SLB backend server. That is, the system adds the node to or removes it from the SLB.
-   When a node label is changed, the system automatically processes the SLB backend server. That is, the system adds the node to or removes it from the SLB.
-   Session sticky is supported.
-   A service created by specifying an existing SLB does not process listeners. You need to add SLB listeners manually.

 |[Access services by using Server Load Balancer](../../../../reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress/Access services by using Server Load Balancer.md#)|


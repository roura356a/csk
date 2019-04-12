# Changes to CloudProvider {#concept_wk1_grd_qfb .concept}

This topic describes the latest changes to CloudProvider.

|Date|Image address|Change|Relevant topic|
|----|-------------|------|--------------|
|March 20, 2019|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.81-gca19cd4-aliyun| -   An SLB instance created in a non-Kubernetes cluster can be reused in a managed or dedicated Kubernetes cluster.
-   A Kubernetes node name can be customized by using Cloud Controller Manager \(CCM\) in addition to setting the Kubernetes NodeName parameter manually.
-   The incompatibility between CCM 1.8.4 and Kubernetes 1.11.5 was fixed. We recommend that you upgrade your CCM to the latest version.

 |[Access services by using Server Load Balancer](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress management/Access services by using Server Load Balancer.md#)|
|December 26, 2018|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.59-ge3bc999-aliyun| Multiple Kubernetes services can reuse the same SLB instance.

-   The SLB instances created by Kubernetes through a service cannot be reused. Otherwise, the reused SLB instances may be removed by accident. Only the SLB instances manually created in the console or created by calling API can be reused.
-   The multiple services that reuse the same SLB instance cannot have the same frontend listening port. Otherwise, port conflicts will occur.
-   If you reuse an SLB instance, you must use the listener name and the virtual server group name to mark the SLB instance. Do not modify the listener names or virtual server group names.
-   You can modify SLB instance names.
-   SLB instances cannot be reused across clusters.

The VPC route table operation method has been changed from concurrent operations to sequential operations. This change helps to avoid the traffic limits in a VPC.

 |[Access services by using Server Load Balancer](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress management/Access services by using Server Load Balancer.md#)|
|August 15, 2018|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun| -   You can use annotation:service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid to specify the primary zone where an automatically created SLB is located.
-   You can use annotation:service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid to specify the secondary zone where an automatically created SLB is located.

**Note:** This parameter does not take effect in regions where a primary zone or secondary zone SLB cannot be created.

-   When you specify an existing SLB, you can use annotation:service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners to override the listeners of the original SLB.
-   You can use annotation：service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth to set a bandwidth value for the created SLB that is billed by bandwidth. Multiple listeners share this bandwidth.

 |[Access services by using Server Load Balancer](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress management/Access services by using Server Load Balancer.md#)|
|June 25, 2018|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3| -   You can set annotation:service.beta.kubernetes.io/alicloud-loadbalancer-backend-label to use the Worker nodes specified by labels as backend servers.
-   You can use annotation：service.beta.kubernetes.io/alicloud-loadbalancer-spec to specify an SLB type. For example, you can set a shared-performance SLB or an exclusive SLB.
-   The service mode externalTrafic: Local is available. Only the node where a pod is located can be added as an SLB backend server.
-   When you add or remove a cluster node, the system automatically processes the SLB backend server. That is, the system adds the node to or removes it from the SLB.
-   When a node label is changed, the system automatically processes the SLB backend server. That is, the system adds the node to or removes it from the SLB.
-   Session persistence is supported.
-   A service created by specifying an existing SLB does not process listeners. You need to add SLB listeners manually.

 |[Access services by using Server Load Balancer](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress management/Access services by using Server Load Balancer.md#)|


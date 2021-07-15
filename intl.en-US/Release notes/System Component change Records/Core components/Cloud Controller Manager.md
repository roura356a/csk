---
keyword: [cloud controller manager, release notes]
---

# Cloud Controller Manager

This topic lists the latest changes to the cloud controller manager.

## Introduction

The cloud controller manager allows you to integrate Kubernetes with Alibaba Cloud services, such as Classic Load Balancer \(CLB\) instances and virtual private clouds \(VPCs\). The cloud controller manager provides features to manage Server Load Balancer \(SLB\) instances and enable cross-node communication.

-   Manage SLB instances

    If you set `Type=LoadBalancer` for a Service, the cloud controller manager automatically creates a CLB instance for the Service, and configures listeners and backend server groups. When the endpoint of an Elastic Compute Service \(ECS\) instance in a vServer group for a Service is changed or the cluster nodes are changed, the cloud controller manager automatically updates the vServer groups of the CLB instance.

-   Enable cross-node communication

    If Flannel is used as the network plug-in of a Kubernetes cluster, the cloud controller manager can enable network connections between containers and nodes. This allows you to implement cross-node communication. The cloud controller manager writes the pod CIDR block to the route table of the VPC where the cluster is deployed. This enables cross-node communication. You can use this feature after the cloud controller manager is installed. No extra configuration is required.


## Usage notes

-   The cloud controller manager automatically creates CLB instances for Services, and configures listeners and backend server groups. For more information, see [Considerations for configuring a LoadBalancer type Service](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Considerations for configuring a LoadBalancer type Service.md).
-   You can also customize load balancing by adding annotations to Service configurations. The cloud controller manager support custom load balancing configurations. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).

## Release notes

**April 2021**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.380-gd6d0962-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.380-gd6d0962-aliyun|2021-04-20|-   The issue that default server groups cannot be updated is fixed.
-   Events are generated and alerts are triggered when an SLB instance has no backend servers. |

**March 2021**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.378-g42eac35-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.378-g42eac35-aliyun|2021-03-08|New features:

-   ECS instances other than those in the Container Service for Kubernetes \(ACK\) cluster can be added to a vServer group.
-   The label `kubernetes.reused.by.user` is automatically added to a reused SLB instance.

Optimizations:

-   The number of the concurrent threads handled by a Service is increased to improve processing speed.
-   The logic of virtual-kubelet nodes is optimized to ignore Service updates caused by the state changes of virtual-kubelet nodes.
-   The node label `service.beta.kubernetes.io/exclude-node` is deprecated. To exclude a node from the management of the cloud controller manager, use the label `service.alibabacloud.com/exclude-node` instead.
-   Resource group verification is required when an SLB instance is reused. The resource group ID specified in the annotations must be the same as the ID of the resource group to which the SLB instance belongs. Otherwise, the SLB instance cannot be reused.
-   The event content is optimized to improve readability.
-   The version priority setting of annotations is optimized. If two versions of an annotation are added to the Service configurations, the later version is prioritized over the earlier version.

Fixed issues:

-   The issue that route entries failed to be deleted due to incomplete node configurations.
-   The logic of node initialization is optimized to fix the issue of taint missing. This prevents pods from being scheduled to a node for which route entries are not created during the initialization process. |

**December 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.339-g9830b58-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.339-g9830b58-aliyun|2020-12-18|-   Hash values are supported in the configurations of LoadBalancer Services. This way, when the cloud controller manager is restarted, only the backend vServer groups of the related SLB instances are updated if the Service configuration is not changed. The configurations of the related SLB instances and listeners are not updated.
-   SLB API calls are optimized to reduce the chances of traffic throttling. |

**September 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.316-g8daf1a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.316-g8daf1a9-aliyun|2020-09-29|-   The occasional failure to update the vServer groups of SLB instances is fixed.
-   The health check port number is changed from 10252 to 10258. |

**August 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.313-g748f81e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.313-g748f81e-aliyun|2020-08-10|-   New features:

-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection can be used to set deletion protection for SLB instances. By default, deletion protection is enabled for newly created SLB instances.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection can be used to set the configuration read-only mode for SLB instances. By default, the configuration read-only mode is enabled for newly created SLB instances.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id can be used to specify the resource group to which an SLB instance belongs. This setting applies only when you create the SLB instance and cannot be modified after the instance is created.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name can be used to specify the name of an SLB instance.
-   You must call API operations of Alibaba Cloud services over internal networks instead of the Internet. To call the cloud controller manager operations, Internet access is no longer required in all regions.
-   A tag is automatically added to an SLB instance that is created for a LoadBalancer Service. The tag is in the `ack.aliyun.com: {your-cluster-id}` format. This feature takes effect only for newly created clusters.
-   The cloud provider ID can be specified in the `<cloudProvider>://<optional>/<segments>/<provider id>` format, which is compatible with open source Kubernetes.
-   Pods are automatically associated with LoadBalancer Services of an ACK cluster that uses Terway after the ACK cluster is created. The pods serve as the backends of the Services. When a LoadBalancer Service is created in a newly created ACK cluster that uses Terway, the pods that are assigned the IP addresses provided by elastic network interfaces \(ENIs\) are added to the backend of the related SLB instance. This improves network performance. LoadBalancer Services do not support the targetPort field that is set to a string value.
-   Improvements:

-   Alpine Linux is upgraded to V3.11.6 for base images.
-   Listener updates are automatically synchronized to vServer groups.
-   SLB API operations are optimized to reduce the time that is required to create an SLB instance by calling the SLB API. |

**June 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.276-g372aa98-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.276-g372aa98-aliyun|2020-06-11|-   New features:

-   You cannot reuse the SLB instances attached to the cluster API server for LoadBalancer Services.
-   Prometheus metrics \(ccm\_node\_latencies\_duration\_milliseconds, ccm\_route\_latencies\_duration\_milliseconds, and ccm\_slb\_latencies\_duration\_milliseconds\) are added to monitor the cloud controller manager latency during synchronizations from Services to SLB instances.
-   Events are added to monitor the synchronization process between a Service and the related SLB instance.
-   Improvements:
    -   Weight calculation is optimized for Services in Local mode. To enable the Local mode, set externalTrafficPolicy=Local in Service configurations. This improves load balancing among pods. For more information, see [How does CCM calculate node weights in Local mode?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/FAQ about Services.md)

    -   API calls of cloud services are optimized to improve the efficiency and reduce the chances of traffic throttling.
    -   When you delete a node with the label service.beta.kubernetes.io/exclude-node, the related route entries are no longer deleted.
-   Fixed issues:
    -   The issue that persistence timeout cannot be set to 0 by adding annotations during Service upgrades.
    -   The issue that bandwidth cannot be set to 100 by adding annotations during Service upgrades. |

**March 2020**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.239-g40d97e1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.239-g40d97e1-aliyun|2020-03-05|-   New features:

For LoadBalancer Services, the cloud controller manager allows you to specify both ECS nodes and ENIs as the backend servers of the related SLB instances.

-   Improvements:
    -   You must call API operations of Alibaba Cloud services over internal networks instead of the Internet. To call the cloud controller manager operations, Internet access is no longer required in regions other than China \(Beijing\), China \(Shanghai\), and UAE \(Dubai\).

    -   The API operation that is used to query VPC route entries is changed to DescribeRouteEntryList. This provides higher performance when hundreds of queries are received within a short period of time. |

**December 2019**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.220-g24b1885-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.220-g24b1885-aliyun|2019-12-31|-   vSwitch IDs are supported. You can set vSwitch IDs in CloudConfig by using the following format: `:vswithid1,:vswitchid2`.
-   Backoff is supported when traffic throttling is enabled. Backoff allows failed requests to rejoin the reconcile queue every 30 to 180 seconds.
-   The number of worker threads to be reconciled is adjusted to 2. This allows you to fully utilize the queries per second \(QPS\) quota on API calls to speed up the reconcile process.
-   The issue that the cloud controller manager quits unexpectedly due to concurrent Map reads and writes based on the aliyungo SDK is fixed.
-   When a node is removed from an ACK cluster, the related route entries are automatically deleted from the VPC route table by the cloud controller manager.
-   The issue that port configurations cannot be changed due to port dependencies for HTTP port forwarding is fixed.
-   If the backend server of an SLB instance is an ECS instance, the serverip field is no longer required when you update the backend server. This prevents errors caused by the changes of default serverip values in API requests when you add backend servers.
-   The related route entries of a node are added to the VPC route table only if the state of the node is known.
-   Network Address Translation \(NAT\) IP addresses are no longer added to node metadata by the cloud controller manager. This fixes the issue that the API server cannot connect to kubelet in occasional cases.
-   When you modify the configurations of a listener, the start listener operation is called only if the listener is in the inactive state. This avoids traffic throttling on API requests. |

**November 2019**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.193-g6cddde4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.193-g6cddde4-aliyun|2019-11-19|-   The label service.beta.kubernetes.io/exclude-node can be added to a node. After the label is added, the node is no longer managed by the cloud controller manager.
-   Multiple pods can be simultaneously added to the backend of an SLB instance. The network type of the pods must be Terway.
-   The node weight cannot be less than 1 for Services in Local mode \(when externalTrafficPolicy=Local is set for the Services\).
-   The issue that vServer groups are repeatedly created when concurrent requests are processed is fixed.
-   The issue that dirty data is generated due to caching when you set node weights is fixed. |

**September 2019**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.164-g2105d2e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3-164-g2105d2e-aliyun|2019-09-11|-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id can be used to renew a certificate.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port can be used to enable port forwarding from an HTTP port to an HTTPS port.
-   The following annotations can be used to create SLB instances with access control list \(ACL\) settings: service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status, service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id, and service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend can be used to remove unschedulable nodes.
-   The annotation service.beta.kubernetes.io/backend-type: "eni" can be used to mount pods in Terway mode to the backend of an SLB instance. This improves the forwarding performance of the network.
-   Services in Local mode \(when externalTrafficPolicy=Local is set for the Services\) can automatically set node weights based on the number of pods on each node. |

**April 2019**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.105-gfd4e547-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.105-gfd4e547-aliyun|2019-04-15|-   Multiple route tables can be created for a VPC. Configuration files can be used to set multiple route tables for a cluster.
-   The issue that updated HTTP configurations do not take effect is fixed. |

**March 2019**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.81-gca19cd4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.81-gca19cd4-aliyun|2019-03-20|-   Existing SLB instances that are not created by ACK can be reused by managed and dedicated Kubernetes clusters.
-   Custom node names are supported. Node naming no longer depends on the nodeName field in Kubernetes.
-   The compatibility issue between cloud controller manager 1.8.4 and Kubernetes 1.11.5 is fixed. We recommend that you upgrade the cloud controller manager to the latest version. |

**December 2018**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.59-ge3bc999-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.59-ge3bc999-aliyun|2018-12-26|-   An SLB instance can be shared by multiple Kubernetes Services.
    -   If an SLB instance is created along with a Service, you cannot reuse this SLB instance when you create other Services. Otherwise, the SLB instance may be deleted. Only SLB instances that are manually created in the console or by calling the API can be used to expose multiple Services.
    -   Services that share the same SLB instance cannot use the same frontend listening port. Otherwise, port conflicts may occur.
    -   When you reuse an SLB instance, you must use the listener name and vServer group name as identifiers. Do not modify the listener name or vServer group name.
    -   You can modify the SLB instance name.
    -   SLB instances cannot be used to expose Services in different clusters.
-   VPC route tables are managed in sequence instead of in parallel. This fixes the traffic throttling issue. |

**August 2018**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.10-gfb99107-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun|2018-08-15|-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid can be used to specify the primary zone for an automatically created SLB instance.
-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid can be used to specify secondary zones for an automatically created SLB instance.

**Note:** This parameter does not take effect in regions that do not support SLB instances deployed across the primary zone and secondary zones.

-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners can be used to overwrite the existing listeners when you reuse an existing SLB instance.
-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth can be used to specify the bandwidth when you create a pay-by-bandwidth SLB instance. The bandwidth is shared among listeners of the SLB instance. |

**June 2018**

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3|2018-06-25|-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-backend-label can be used to add worker nodes with specific labels as the backend servers of an SLB instance.
-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-spec can be used to specify the SLB instance type, such as the shared-resource or high-performance instance type.
-   The setting `externalTraffic: Local` is supported in Service configurations. If this mode is enabled, only nodes that host the pods are added as the backend servers of the related SLB instance.
-   If a node is added to or removed from a cluster, the node is automatically added to or removed from the backend servers of the related SLB instances.
-   When the labels of a node are changed, the node is automatically added to or removed from the backend servers of the related SLB instances.
-   Sticky sessions are supported.
-   Listeners are no longer managed by the system when you create a Service by using an existing SLB instance. You must manually add listeners. |


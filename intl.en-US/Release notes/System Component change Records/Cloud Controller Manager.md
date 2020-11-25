---
keyword: [CCM, release notes]
---

# Cloud Controller Manager

This topic lists the latest changes to Cloud Controller Manager \(CCM\).

## September 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.316-g8daf1a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.316-g8daf1a9-aliyun|September 29, 2020|-   The occasional failure to update Server Load Balancer \(SLB\) VServer groups is fixed.
-   The health check port is changed from port 10252 to port 10258. |

## August 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.313-g748f81e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.313-g748f81e-aliyun|August 10, 2020|-   New features:

-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection can be used to set deletion protection for SLB instances. By default, deletion protection is enabled for new SLB instances.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection can be used to set the configuration read-only mode. By default, configuration read-only mode is enabled for new SLB instances.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id can be used to specify the resource group to which an SLB instance belongs. This setting applies only when you create the SLB instance and cannot be modified.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name can be used to specify a name for an SLB instance.
-   You must call API operations of Alibaba Cloud services over internal networks instead of the Internet. To call CCM operations, Internet access is no longer required in all regions.
-   Tags are added to an SLB instance that is created for the LoadBalancer service. The tags are in the `ack.aliyun.com: {your-cluster-id}` format. This feature applies to only new clusters.
-   Cloud provider ID is in the following format: `<cloudProvider>://<optional>/<segments>/<provider id>`.
-   Pods are specified as backend servers when you create a LoadBalancer service in a Terway cluster. When you create a new Terway Container Service for Kubernetes \(ACK\) cluster, if a LoadBalancer service is created, the pods that are assigned the elastic network interfaces \(ENIs\) IP addresses are specified as backend servers. This feature improves network performance. The LoadBalancer service does not support the targetPort field that is set to a string value.
-   Improvements:

-   Alpine Linux is upgraded to V3.11.6.
-   Listener updates are synchronized to VServer groups.
-   SLB API operations are optimized to reduce the time that is required to create an SLB instance. |

## June 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.276-g372aa98-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.276-g372aa98-aliyun|June 11, 2020|-   New features:

-   You cannot reuse the SLB instances of the cluster API Server for LoadBalancer services.
-   Prometheus metrics \(ccm\_node\_latencies\_duration\_milliseconds, ccm\_route\_latencies\_duration\_milliseconds, and ccm\_slb\_latencies\_duration\_milliseconds\) are added to monitor information about the CCM synchronization delay from services to SLB instances.
-   Events are added to monitor the synchronization process between the service and LoadBalancer.
-   Improvements:
    -   Weight calculation is optimized for services in Local mode. To enable the Local mode, set externalTrafficPolicy to Local. This improves load balancing among pods. For more information, see [How does CCM calculate node weights in Local mode?](/intl.en-US/User Guide for Kubernetes Clusters/Network management/FAQ.md)

    -   API requests for cloud services are optimized to improve efficiency and reduce traffic throttling.
    -   When you delete a node to which the service.beta.kubernetes.io/exclude-node label is added, the associated Ingress is no longer deleted.
-   Fixed issues:
    -   The following issue is fixed: persistence timeout cannot be set to 0 based on annotations during service upgrades.
    -   The following issue is fixed: bandwidth cannot be set to 100 based on annotations during service upgrades. |

## March 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.239-g40d97e1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.239-g40d97e1-aliyun|March 5, 2020|-   New features:

For LoadBalancer services, CCM allows you to specify both Elastic Compute Service \(ECS\) instance-based nodes and elastic network interfaces \(ENIs\) as backend servers of SLB instances.

-   Improvements:
    -   You must call API operations of Alibaba Cloud services over internal networks instead of the Internet. To call CCM operations, Internet access is no longer required in regions other than China \(Beijing\), China \(Shanghai\), and UAE \(Dubai\).

    -   The API operation to query VPC route entries is changed to DescribeRouteEntryList. This provides higher performance when more than 100 queries are received within a short period of time. |

## December 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.220-g24b1885-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.220-g24b1885-aliyun|December 31, 2019|-   Support for VSwitchids is added. You can set VSwitchids in CloudConfig by using the following format: `:vswithid1,:vswitchid2`.
-   Support for Backoff is added when traffic throttling is enabled. Backoff allows failed requests to rejoin the reconcile queue every 30 to 180 seconds.
-   The number of worker threads is adjusted to be reconciled to 2. This allows you to fully utilize the queries per second \(QPS\) quota on API calls to accelerate the reconcile queue.
-   The system error that is caused by concurrent Map reads and writes based on the aliyungo SDK is fixed.
-   When a node is removed from an ACK cluster, CCM automatically deletes the related Virtual Private Cloud \(VPC\) route entries from the route table.
-   The following issue is fixed: Port configurations cannot be changed due to port dependencies that are used for HTTP port forwarding.
-   If the backend server of an SLB instance is an ECS instance, the serverip field is no longer required when you update the backend server. This avoids errors caused by different default serverip values of API requests when you add backend servers.
-   Only when the status of a node is known, the related VPC route entries are added to the route table.
-   CCM no longer adds Network Address Translation \(NAT\) IP addresses to node metadata. This fixes the issue when APIServer cannot connect to kubelet in specific cases.
-   When you modify the configurations of a listener, the start listener operation is called only when the listener status is inactive. This avoids traffic throttling on API requests. |

## November 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.193-g6cddde4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.193-g6cddde4-aliyun|November 19, 2019|-   The label service.beta.kubernetes.io/exclude-node can be added to a node. In this case, CCM is no longer used to manage the node.
-   Multiple pods can be simultaneously added to the backend servers of an SLB instance. This requires that the pod network type is Terway.
-   The node weight cannot be less than 1 in Local mode when externalTrafficPolicy is set to Local.
-   The following issue is fixed: VServer groups are repeatedly created when concurrent requests are processed.
-   The following issue is fixed: Dirty data is generated due to caching when you set node weights. |

## September 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.164-g2105d2e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3-164-g2105d2e-aliyun|September 11, 2019|-   A certificate can be updated based on the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id.
-   Port forwarding from HTTP to HTTPS can be enabled based on the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port.
-   An SLB instance can be created with access control list \(ACL\) settings based on the following annotations: service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status, service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id, and service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type.
-   Unschedulable nodes can be removed based on the annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend.
-   Pods can be mounted to the backend servers of an SLB instance based on the annotation service.beta.kubernetes.io/backend-type: "eni" in Terway mode. This improves network forwarding performance.
-   A service can automatically set the node weight based on the number of pods on the node in Local mode when externalTrafficPolicy is set to Local. |

## April 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.105-gfd4e547-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.105-gfd4e547-aliyun|April 15, 2019|-   Multiple route tables can be created in a virtual private cloud \(VPC\). The configuration file can be used to set multiple route tables for a cluster.
-   The following issue is fixed: Updated HTTP configurations do not take effect. |

## March 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.81-gca19cd4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.81-gca19cd4-aliyun|March 20, 2019|-   Existing SLB instances can be reused by managed and dedicated Kubernetes clusters. These SLB instances are not created in ACK.
-   Custom node names are supported. Node naming no longer depends on the nodeName field in ACK.
-   The compatibility issue between CCM V1.8.4 and Kubernetes V1.11.5 is fixed. We recommend that you upgrade CCM to the latest version. |

## December 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.59-ge3bc999-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.59-ge3bc999-aliyun|December 26, 2018|-   Multiple Kubernetes services can reuse the same SLB instance.
    -   If an SLB instance is created when you create a service, you cannot reuse this SLB instance when you create other services. Otherwise, the SLB instance may be deleted. You can reuse only the SLB instances that are manually created in the console or by calling API operations.
    -   Services that reuse the same SLB instance cannot have the same frontend listening port. Otherwise, port conflicts may occur.
    -   When you reuse an SLB instance, you must use the listener name and VServer group name as identifiers. Do not modify the listener name or VServer group name.
    -   You can modify the name of the SLB instance.
    -   You cannot reuse SLB instances across clusters.
-   The traffic throttling issue is fixed when VPC route tables are managed in sequence, instead of being managed in parallel. |

## August 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.10-gfb99107-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun|August 15, 2018|-   The primary zone can be specified to deploy automatically created SLB instances based on the annotation service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid.
-   The secondary zone can be specified to deploy automatically created SLB instances based on the annotation service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid.

**Note:** This parameter does not take effect in regions where SLB instances across primary and secondary zones are not supported.

-   The existing listeners can be overwritten when you reuse an SLB instance based on the annotation service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners.
-   The bandwidth can be specified when you create a pay-by-bandwidth SLB instance based on the annotation service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth. The bandwidth is shared among listeners of the SLB instance. |

## June 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3|June 25, 2018|-   Worker nodes can be added with specific labels and specified as backend servers based on the annotation service.beta.kubernetes.io/alicloud-loadbalancer-backend-label.
-   The type of SLB instance can specified, such as shared-performance or exclusive. This is based on annotation service.beta.kubernetes.io/alicloud-loadbalancer-spec.
-   Services can be set to the Local mode based on the configuration of `externalTrafic: Local`. When this mode is enabled, only nodes that host pods can be added as the backend servers of the SLB instance that is attached to the cluster.
-   When a node is added to or removed from a cluster, the system automatically adds the node to or removes it from the backend servers of the SLB instance that is attached to the cluster.
-   When the label of a node is changed, the system automatically adds the node to or removes it from the backend servers of the SLB instance that is attached to the cluster.
-   Sticky sessions are supported.
-   When you create a service based on an existing SLB instance, the system no longer manages listeners. You must manually add listeners. |


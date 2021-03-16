---
keyword: [CCM, release note]
---

# Cloud Controller Manager

This topic lists the latest changes to Cloud Controller Manager \(CCM\).

## December 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.339-g9830b58-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.339-g9830b58-aliyun|December 18, 2020|-   Hash values are supported in the configurations of LoadBalancer type Services. This way, when CCM is restarted, only the backend vServer groups of the related Server Load Balancer \(SLB\) instances are updated if the Service configuration is not changed. The configurations of the related SLB instances and listeners are not updated.
-   SLB API calls are optimized to reduce the chances of traffic throttling. |

## September 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.316-g8daf1a9-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.316-g8daf1a9-aliyun|September 29, 2020|-   The occasional failure to update the vServer groups of SLB instances is fixed.
-   The health check port number is changed from 10252 to 10258. |

## August 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.313-g748f81e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.313-g748f81e-aliyun|August 10, 2020|-   New features:

-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-delete-protection can be used to set deletion protection for SLB instances. By default, deletion protection is enabled for newly created SLB instances.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-modification-protection can be used to set the configuration read-only mode for SLB instances. By default, the configuration read-only mode is enabled for newly created SLB instances.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-resource-group-id can be used to specify the resource group to which an SLB instance belongs. This setting applies only when you create the SLB instance and cannot be modified after the instance is created.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-name can be used to specify the name of an SLB instance.
-   You must call API operations of Alibaba Cloud services over internal networks instead of the Internet. To call CCM operations, Internet access is no longer required in all regions.
-   Tags are added to an SLB instance that is created for a LoadBalancer type Service. The tags are in the `ack.aliyun.com: {your-cluster-id}` format. This feature applies to only newly created clusters.
-   The cloud provider ID can be specified in the `<cloudProvider>://<optional>/<segments>/<provider id>` format, which is compatible with the Kubernetes community.
-   Pods are added to the backend of the SLB instances that are created for LoadBalancer type Services in newly created Container Service for Kubernetes \(ACK\) clusters that use Terway as the network plug-in. When a LoadBalancer type Service is created in a newly created ACK cluster that uses Terway, the pods that are assigned the IP addresses provided by elastic network interfaces \(ENIs\) are added to the backend of the related SLB instance. This improves network performance. LoadBalancer type Services do not support the targetPort field that is set to a string value.
-   Improvements:

-   Alpine Linux is upgraded to V3.11.6 for basic images.
-   Listener updates are automatically synchronized to vServer groups.
-   SLB API operations are optimized to reduce the time that is required to create an SLB instance. |

## June 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.276-g372aa98-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.276-g372aa98-aliyun|June 11, 2020|-   New features:

-   You cannot reuse the SLB instances of the cluster API server for LoadBalancer type Services.
-   Prometheus metrics \(ccm\_node\_latencies\_duration\_milliseconds, ccm\_route\_latencies\_duration\_milliseconds, and ccm\_slb\_latencies\_duration\_milliseconds\) are added to monitor the CCM synchronization latencies from Services to SLB instances.
-   Events are added to monitor the synchronization process between a Service and the related SLB instance.
-   Improvements:
    -   Weight calculation is optimized for Services in Local mode. To enable the Local mode, set externalTrafficPolicy=Local in Service configurations. This improves load balancing among pods. For more information, see [How does CCM calculate node weights in Local mode?](/intl.en-US/User Guide for Kubernetes Clusters/Network/FAQ.md)

    -   API calls of cloud services are optimized to improve efficiency and reduce the chances of traffic throttling.
    -   When you delete a node with the service.beta.kubernetes.io/exclude-node label, the associated Ingress is no longer deleted.
-   Fixed issues:
    -   The following issue is fixed: persistence timeout cannot be set to 0 by adding annotations during Service upgrades.
    -   The following issue is fixed: bandwidth cannot be set to 100 by adding annotations during Service upgrades. |

## March 2020

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.239-g40d97e1-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.239-g40d97e1-aliyun|March 5, 2020|-   New features:

For LoadBalancer type Services, CCM allows you to specify both Elastic Compute Service \(ECS\) instance-based nodes and ENIs as the backend servers of the related SLB instances.

-   Improvements:
    -   You must call API operations of Alibaba Cloud services over internal networks instead of the Internet. To call CCM operations, Internet access is no longer required in regions other than China \(Beijing\), China \(Shanghai\), and UAE \(Dubai\).

    -   The API operation to query virtual private cloud \(VPC\) route entries is changed to DescribeRouteEntryList. This provides higher performance when hundreds of queries are received within a short period of time. |

## December 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.220-g24b1885-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64: v1.9.3.220-g24b1885-aliyun|December 31, 2019|-   vSwitch IDs are supported. You can set vSwitch IDs in CloudConfig by using the following format: `:vswithid1,:vswitchid2`.
-   Backoff is supported when traffic throttling is enabled. Backoff allows failed requests to rejoin the reconcile queue every 30 to 180 seconds.
-   The number of worker threads to be reconciled is adjusted to 2. This allows you to fully utilize the queries per second \(QPS\) quota on API calls to speed up the reconcile process.
-   The system error that is caused by concurrent Map reads and writes based on the aliyungo SDK is fixed.
-   When a node is removed from an ACK cluster, CCM automatically deletes the related VPC route entries from the route table.
-   The following issue is fixed: Port configurations cannot be changed due to port dependencies that are used for HTTP port forwarding.
-   If the backend server of an SLB instance is an ECS instance, the serverip field is no longer required when you update the backend server. This avoids errors caused by the changes of default serverip values of API requests when you add backend servers.
-   The related VPC route entries are added to the route table only when the state of a node is known.
-   CCM no longer adds Network Address Translation \(NAT\) IP addresses to node metadata. This fixes the issue that API Server cannot connect to kubelet in occasional cases.
-   When you modify the configurations of a listener, the start listener operation is called only when the listener is in the inactive state. This avoids traffic throttling on API requests. |

## November 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.193-g6cddde4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.193-g6cddde4-aliyun|November 19, 2019|-   The label service.beta.kubernetes.io/exclude-node can be added to a node. After the label is added, the node is no longer managed by CCM.
-   Multiple pods can be simultaneously added to the backend of an SLB instance. The network type of the pods must be Terway.
-   The node weight cannot be less than 1 for Services in Local mode \(when externalTrafficPolicy=Local is set for the Services\).
-   The issue that vServer groups are repeatedly created when concurrent requests are processed is fixed.
-   The issue that dirty data is generated due to caching when you set node weights is fixed. |

## September 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.164-g2105d2e-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3-164-g2105d2e-aliyun|September 11, 2019|-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id can be used to renew a certificate.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port can be used to enable port forwarding from an HTTP port to an HTTPS port.
-   The following annotations can be used to create SLB instances with access control list \(ACL\) settings: service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-status, service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-id, and service.beta.kubernetes.io/alibaba-cloud-loadbalancer-acl-type.
-   The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-remove-unscheduled-backend can be used to remove unschedulable nodes.
-   The annotation service.beta.kubernetes.io/backend-type: "eni" can be used to mount pods in Terway mode to the backend of an SLB instance. This improves the forwarding performance of the network.
-   Services in Local mode \(when externalTrafficPolicy=Local is set for the Services\) can automatically set node weights based on the number of pods on each node. |

## May 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.110-g4938309-aliyun|registry.cn-beijing.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.110-g4938309-aliyun|May 30, 2019|-   Parameters can be set to disable the creation of Internet-facing SLB instances.
-   The ID of a certificate can be changed.

The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-cert-id can be used to change the ID of a certificate.

-   A vSwitch can be specified when you attach an internal-facing SLB instance to a Service.

The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-vswitch-id can be used to specify a vSwitch.

-   The Describe ECS operation can be called in batches. This avoids traffic throttling for this operation when the number of ECS instance-based nodes reaches the upper limit.
-   Allows you to set SLB instance configurations to redirect requests from HTTP port 80 to HTTPS port 443.

The annotation service.beta.kubernetes.io/alibaba-cloud-loadbalancer-forward-port can be used to configure port forwarding. |

## April 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.105-gfd4e547-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.105-gfd4e547-aliyun|April 15, 2019|-   Multiple route tables can be created in a VPC. The configuration file can be used to set multiple route tables for a cluster.
-   The issue that updated HTTP configurations do not take effect is fixed. |

## March 2019

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.81-gca19cd4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.81-gca19cd4-aliyun|March 20, 2019|-   Existing SLB instances that are not created by ACK can be reused by managed and dedicated Kubernetes clusters.
-   Custom node names are supported. Node naming no longer depends on the nodeName field in Kubernetes.
-   The compatibility issue between CCM 1.8.4 and Kubernetes 1.11.5 is fixed. We recommend that you upgrade CCM to the latest version. |

## December 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.59-ge3bc999-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.59-ge3bc999-aliyun|December 26, 2018|-   An SLB instance can be shared by multiple Kubernetes Services.
    -   If an SLB instance is created when you create a Service, you cannot reuse this SLB instance when you create other Services. Otherwise, the SLB instance may be deleted. You can reuse only SLB instances that are manually created in the console or by calling the API.
    -   Services that share the same SLB instance cannot use the same frontend listening port. Otherwise, port conflicts may occur.
    -   When you reuse an SLB instance, you must use the listener name and vServer group name as identifiers. Do not modify the listener name or vServer group name.
    -   You can modify the SLB instance name.
    -   You cannot reuse SLB instances across clusters.
-   VPC route tables are managed in sequence instead of in parallel. This fixes the traffic throttling issue. |

## August 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3.10-gfb99107-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3.10-gfb99107-aliyun|August 15, 2018|-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-master-zoneid can be used to specify the primary zone for an automatically created SLB instance.
-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-slave-zoneid can be used to specify the secondary zones for an automatically created SLB instance.

**Note:** This parameter does not take effect in regions that do not support SLB instances that are deployed across primary and secondary zones.

-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-force-override-listeners can be used to overwrite the existing listeners when you reuse an SLB instance.
-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth can be used to specify the bandwidth when you create a pay-by-bandwidth SLB instance. The bandwidth is shared among listeners of the SLB instance. |

## June 2018

|Version|Image address|Release date|Description|
|-------|-------------|------------|-----------|
|v1.9.3|registry.cn-hangzhou.aliyuncs.com/acs/cloud-controller-manager-amd64:v1.9.3|June 25, 2018|-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-backend-label can be used to add worker nodes with specific labels as the backend servers of SLB instances.
-   The annotation service.beta.kubernetes.io/alicloud-loadbalancer-spec can be used to specify the SLB instance types, such as the shared-resource or high-performance instance types.
-   The configuration of `externalTrafic: Local` can be used to set the Local mode for Services. When this mode is enabled, only nodes that host pods can be added as the backend servers of the SLB instance that is attached to the cluster.
-   When a node is added to or removed from a cluster, the system automatically adds the node to or removes it from the backend servers of the SLB instance that is attached to the cluster.
-   When the labels of a node are changed, the system automatically adds the node to or removes it from the backend servers of the SLB instance that is attached to the cluster.
-   Sticky sessions are supported.
-   When you create a Service by using an existing SLB instance, the system no longer manages listeners. You must manually add listeners. |


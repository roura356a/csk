# Changes to Alibaba Cloud Container Service for Kubernetes {#concept_qrb_znb_yfb .concept}

This topic describes the changes to Alibaba Cloud Container Service for Kubernetes \(ACK\).

|Date|Type|Change|Relevant topic|
|----|----|------|--------------|
|February 2019|New region|For Alibaba Cloud users based in Mainland China and outside Mainland China, the cluster type of managed Kubernetes is released in the China South 1 \(Shenzhen\) region.This cluster type offers the following advantages:

-   Resources are reserved by default. Specifically, each managed Kubernetes cluster reserves you three Master nodes.
-   Cluster O&M is easy. Alibaba Cloud Container Service for Kubernetes \(ACK\) creates and manages Master nodes for each cluster.
-   Highly secure clusters. ACK secures your clusters according to your requirements.

|N/A|
|New feature| ACK released a Knative add-on to help you quickly build a Knative Serving environment in an ACK supported Kubernetes cluster.

 Knative provides a request-driven computing model in which workloads can be scaled back to zero. Based on Kubernetes and Istio, it provides deployment and service features for serverless applications and functions.

 |N/A|
|New feature|For Alibaba Cloud users based in Mainland China, the intelligent cluster O&M feature is released across all regions..With this feature, you can check your cluster resources, components, configurations, and other information to quickly troubleshoot your cluster problems.

|N/A|
|January 2019|New feature|Containerized applications in Windows is released \(beta version\).To use this feature, you need to add Windows nodes to your dedicated Kubernetes clusters or managed Kubernetes clusters, which run in ACK.

To request access to this feature, open a ticket.

 |N/A|
|New feature|Container Registry \(Enterprise edition\) is released \(beta version\).This edition allows you to:

-   Deploy a container image repository that is built on exclusive resources.
-   Build, distribute, and host your images in a fully secure environment with ease.

We recommend that you use Container Registry \(Enterprise edition\) if you have a large number of cluster nodes and require a higher level of security.

To request access to this feature, open a ticket.

|N/A|
|New feature|The intelligent cluster O&M function is released in the China East 1 \(Hangzhou\) region.This feature provides the best solutions for container cluster users in different scenarios.

With this feature, you can check your cluster resources, components, configurations, and other information to quickly find out your cluster problems.

|N/A|
|New feature|ACK is interoperable with Application Real-Time Monitoring Service \(ARMS\).After you install the ARMS plugin in your cluster, you can easily monitor the performance of the applications deployed in the cluster.

ARMS is a Java application performance management \(APM\) monitoring product developed by Alibaba Cloud. If you use ARMS to monitor a Java application, you only need to mount a probe in the application startup script without modifying any code. By comprehensively monitoring the application, the probe helps you quickly locate faulty and slow interfaces, reproduce parameter calling, detect memory leaks, and discover system bottlenecks, more efficiently diagnosing problems online.

|N/A|
|Commercialized product|Charges for Elastic Container Instance \(ECI\) used by serverless Kubernetes is introduced.From January 22, 2019 on, you will be charged if you use a serverless Kubernetes cluster to create container instances. However, serverless Kubernetes can be still used free of charge.

|For more information, see [Billing method](https://www.alibabacloud.com/help/zh/doc-detail/89142.html).|
|New regions|For Alibaba Cloud users in Mainland China, the cluster type of serverless Kubernetes is released for the following regions: China North 2 \(Beijing\) and China South 1 \(Shenzhen\).|N/A|
|December 2018|New region|The UK \(London\) region is added as a region for ACK services for Alibaba Cloud users in Mainland China and outside Mainland China.|N/A|
|New region|The cluster type of managed Kubernetes is released for the following regions: China East 2 \(Shanghai\), Asia Pacific SE 3 \(Kuala Lumpur\), and Asia Pacific SOU 1 \(Mumbai\).|N/A|
|New feature|Nodes can be removed from a Kubernetes cluster.Furthermore, you can determine whether to release the ECS instance used by the node when you remove a node.

|[Remove a node](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Node management/Remove a node.md#).|
|New feature|Creating an application of the DaemonSet type is supported. DaemonSet \(a type of daemon\) guarantees that each node of a cluster has only one pod.

|N/A|
|New feature|Support for customizing Istio gateway parameters is added.You can set different parameters to customize Istio ingress and egress gateways.

| N/A

 |
|New feature|Istio CoreDNS is supported.| N/A

 |
|New feature|Support for using existing ECS instances to create a managed Kubernetes cluster is added.|[Create a managed Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#).|
|November 2018|New region|The cluster type of managed Kubernetes is released for the Asia Pacific SE 5 \(Jakarta\) region.|N/A|
|Commercialized product|The Terway network plugin is released.This type of network plugin has higher performance than the Flannel network plugin. If you select Terway as your cluster network plugin, containers within the cluster can directly communicate with each other through the Elastic Network Interface \(ENI\).

|[Terway network plugin](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Network management/Terway network plugin.md#).|
|New feature|Added a performance metric thumbnail of each Worker node to easily check the node status.|N/A|
|New feature| Support for multiple existing nodes to be added to a cluster at one time is provided.

 |N/A|
|New feature| Support for updating Kubernetes cluster certificates that are about to expire is provided.

 |N/A|
|October 2018|New region| The Shenzhen Financial Cloud region is released.

 Alibaba Cloud Container Service for Kubernetes is available in Shenzhen Financial Cloud and is guaranteed by the security and compliance of the Financial Cloud.

 |N/A|
|New feature|The managed Kubernetes is released.|N/A|
|New feature|Application management is optimized. You can manage deployment versions of applications. For example, you can roll back to an earlier version.|N/A|
|New feature|Istio can be integrated with Alibaba Cloud Container Service for Kubernetes.|For more information, see [Overview](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Istio management/Overview.md#).|
|September 2018|New feature|Alibaba Cloud Container Service for Kubernetes 1.11 is available.-   New feature includes CRD upgrade, CoreDNS GA, pod priority setting, and preemptive scheduling.
-   You can select v1.10 or v1.11 as needed.
-   You can configure multiple containers and create StatefulSet applications in the Container Service console.

|[Create a StatefulSet application by using an image](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a StatefulSet application by using an image.md#)|
|New feature|With Alibaba Cloud Container Service for Kubernetes, you can pull an image without a password from Container Registry.| |
|New feature|Automatic node scaling is available.|[Cluster auto scaling](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Cluster auto scaling.md#)|
|New feature|Preemptible instances are available.| |
|August 2018|New feature|The managed Kubernetes is released for beta.|[Create a managed Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#)|
|New feature|The Istio addon plugin is available.|N/A|
|July 2018|New region|The Australia region is available for Alibaba Cloud Container Service for Kubernetes.|N/A|
|New feature|Gray releases and batch releases are available.|For more information, see [Gray releases and blue/green deployment](../../../../../reseller.en-US/Best Practices/Release/Implement a gray release and a blue__green deployment through Ingress in a Kubernetes cluster/Gray releases and blue__green deployment.md#) and [Use batch release on Alibaba Cloud Container Service for Kubernetes](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Release management/Use batch release on Alibaba Cloud Container Service for Kubernetes.md#).|
|June 2018|New region|Asia Pacific NE 1 \(Tokyo\) and China North 5 \(Hohhot\) are available.|N/A|
|New feature|Alibaba Cloud Container Service for Kubernetes 1.10 supports FPGA and HugePages.|N/A|
|New feature|The monitoring function is upgraded. Specifically, application monitoring and alarm notifications can be configured.|[Integration and usage with CloudMonitor](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Monitoring management/Integration and usage with CloudMonitor.md#)|
|New feature|You can create Subscription clusters.|[Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#)|
|New feature|In Serverless Kubernetes clusters, new kubectl commands, namely, kubectl exec and kubectl attach can be run, and the Ingress function is available.|[Kubernetes supported functions](../../../../../reseller.en-US/User Guide/Serverless Kubernetes cluster/Kubernetes supported functions.md#)|
|May 2018|New region| Shanghai Financial Cloud is available.

 Alibaba Cloud Container Service for Kubernetes is available in Shanghai Financial Cloud and is guaranteed by the security and compliance of the Financial Cloud.

 |N/A|
|New feature|Serverless Kubernetes is released in Alibaba Cloud Container Service for Kubernetes.|[Serverless Kubernetes cluster](reseller.en-US/Product Introduction/Serverless Kubernetes cluster.md#)|
|New feature|Blue/green deployment, gray releases, and A/B testing are available.|For more information, see [Gray releases and blue/green deployment](../../../../../reseller.en-US/Best Practices/Release/Implement a gray release and a blue__green deployment through Ingress in a Kubernetes cluster/Gray releases and blue__green deployment.md#).|
|April 2018|New region| India \(Mumbai\), Indonesia \(Jakarta\), and Dubai are available.

 Five new regions covering the Southeast Asia, the Middle East, and India are available, and can use v1.9 that is the latest and stable version.

 |N/A|
|New feature| Multi-zone Kubernetes is released for Alibaba Cloud Container Service for Kubernetes. You can create highly available Kubernetes clusters.

 You can deploy Master nodes in three zones, and deploy Worker nodes in multiple zones. With this new feature, you can make the best use of Kubernetes clusters for production.

 |[Create a multi-zone Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a multi-zone Kubernetes cluster.md#)|
|New feature|The service catalog function is updated to support MySQL, RDS, RabbitMQ, and Spark.|For more information, see [Overview](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Service catalog management/Overview.md#) of service catalog management.|
|New feature|You can manage applications released by using Helm.|[Manage a Heml-based release](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Release management/Manage a Helm-based release.md#)|
|March 2018|New feature| Alibaba Cloud Container Service for Kubernetes 1.9 is available. With this version, you can customize ECS images.

 Alibaba Cloud Container Service for Kubernetes supports native Kubernetes 1.9.3. Workloads API is released. CRD is enabled by default. GPU scheduling is supported. In addition, you can customize ECS images when creating a cluster. You can add nodes to the cluster by automatically reset images.

 |N/A|
|New feature|Alibaba Cloud Container Service for Kubernetes has released app catalog with which you can implement one-click application deployment through Helm.|[Manage a Heml-based release](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Release management/Manage a Helm-based release.md#)|
|New feature|Alibaba Cloud Container Service for Kubernetes has released service catalog which supports ServiceBroker.|For more information, see [Overview](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Service catalog management/Overview.md#) of service catalog management.|
|New feature|Alibaba Cloud Container Service for Kubernetes has upgraded resource monitoring. Specifically, you can monitor cluster nodes through CloudMonitor.|[Group-based monitoring and alarms](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Monitoring management/Group-based monitoring and alarms.md#)|
|January 2018|New region|The international Websites of both Alibaba Cloud Container Registry and Alibaba Cloud Container Service for Kubernetes are available.|N/A|
|New feature|Alibaba Cloud Container Service for Kubernetes 1.8.4 is released to provide security upgrade and auto scaling.|[Cluster auto scaling](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Cluster auto scaling.md#)|
|New feature|FlexVolume is released and supports cloud disks, NAS, and OSS.| [Use Alibaba Cloud cloud disk volumes](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use Alibaba Cloud cloud disk volumes.md#)

 [Use NAS file systems of Alibaba Cloud](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use NAS file systems of Alibaba Cloud.md#)

 [Use Alibaba Cloud OSS volumes](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use Alibaba Cloud OSS volumes.md#)

 |
|New feature|Network functions is upgraded to support network policies and bandwidth limits.|[Access services by using Server Load Balancer](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress management/Access services by using Server Load Balancer.md#)|
|New feature|ECS Bare Metal \(EBM\) instances are available.|N/A|
|October 2017|New feature| The native Kubernetes version is now available.

 Alibaba Cloud Container Service for Kubernetes 1.8.1 is available.

 |[What is Container Service for Kubernetes](reseller.en-US/Product Introduction/What is Container Service for Kubernetes.md#)|
|New feature|Alibaba Cloud Container Service blockchain solution is in beta.|For more information, see [Overview](../../../../../reseller.en-US/solution/Blockchain solution/Overview.md#) of blockchain solution.|
|August 2017|New feature|Alibaba Cloud Container Service for Kubernetes 1.7.2 is available.|N/A|


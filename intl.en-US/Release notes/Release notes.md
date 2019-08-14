# Release notes {#concept_1443432 .concept}

This topic provides a summary of the release notes of Alibaba Cloud Container Service for Kubernetes \(ACK\).

## June 2019 {#section_le6_9kd_l9w .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for Managed Kubernetes|More regions have been added to support Managed Kubernetes. Starting from June 2019, you can use Managed Kubernetes in Japan \(Tokyo\) and UK \(London\).| Japan \(Tokyo\)

 UK \(London\)

 |[EN-US\_TP\_15496.md\#](intl.en-US/Product Introduction/Alibaba Cloud Container Service for Kubernetes Overview.md#)|
|High-density deployment supported by the new Terway plug-in|The new version of the Terway plug-in is available. It supports both the exclusive ENI mode and the inclusive ENI mode. The default mode is the inclusive ENI mode. -   Exclusive ENI mode: In this mode, the number of deployed pods must match the number of the created ENIs on each node. This mode can improve network performance.
-   Inclusive ENI mode: In this mode, you can have multiple container instances corresponding to one ENI. This mode improves the container deployment density of a single node.

 |All|[Terway network plugin](../../../../intl.en-US/User Guide for Kubernetes Clusters/Network management/Terway network plugin.md#)|
|Adding support for the Knative framework|Knative is a serverless framework based on Kubernetes. Knative is used to create a serverless orchestration standard for deploying cloud-native and cross-platform services. Knative implements this serverless standard by integrating container or function creation, workload management \(auto scaling\), and event models. ACK introduces the Knative deployment feature, which can help to install and upgrade the Build, Serving, and Eventing components. You must deploy Istio before using the Knative feature. A wizard for deploying sample applications and solutions for tracing, monitoring, and logging are also provided.|All|[Knative overview](../../../../intl.en-US/User Guide for Kubernetes Clusters/Knative management/Knative overview.md#)[Use Knative to deploy a Hello World application](../../../../intl.en-US/User Guide for Kubernetes Clusters/Knative management/Use Knative to deploy a Hello World application.md#)|
|Adding support to search by node IP addresses and pod IP addresses|Searching for a pod by node IP address and pod IP address is supported. In the management console, choose **Application** \> **Pods** and specify the node IP address or pod IP address to manage a pod.|All|None.|

## May 2019 {#section_zob_k9s_n09 .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for Managed Kubernetes on Alibaba Cloud and Alibaba Finance Cloud|More regions have been added to support the Managed Kubernetes on Alibaba Cloud and Alibaba Finance Cloud. Starting from May 2019, you can use Managed Kubernetes on Alibaba Cloud in Australia \(Sydney\) and use Managed Kubernetes on Alibaba Finance Cloud in China \(Shanghai\).

 | Australia \(Sydney\)

 China \(Shanghai\)

 |[EN-US\_TP\_15496.md\#](intl.en-US/Product Introduction/Alibaba Cloud Container Service for Kubernetes Overview.md#)|
|Genomics computing cluster that is designed for genomics computing|Container Service has launched the genomics computing clusters for Dedicated Kubernetes. The clusters contain the worker nodes that use high-performance ECS instances and supports the workflow engine of high specification. It allows you to process a large amount of batch genomics computing tasks in the following file formats: BCL, FASTQ, BAM, SAM, and VCF. Genomic computing tasks include genomic data analysis, alignment, and mutation detection. In the management console, choose **Clusters** \> **Clusters**, and click Create Kubernetes Cluster. On the Select Cluster Template page, select the **Dedicated Genomics Computing Cluster** template to create the Dedicated Genomics Kubernetes cluster.|All|None.|
|Adding support for image and video encoding acceleration in FPGA clusters|Container Service has launched FPGA clusters, which use FPGA F3 instances as worker nodes. An FPGA cluster is suitable to perform video and image computing tasks, such as encoding videos with the H.265 standard and converting JPEG images to HEIF images. The time spent on implementing FPGA solutions of video encoding is reduced from one week to 15 minutes. This significantly saves the CDN bandwidth for the bitrate of transcoding and optimization of non-compression video quality. In the management console, choose **Clusters** \> **Clusters**, and click Create Kubernetes Cluster. On the Select Cluster Template page, select the **Dedicated FPGA Cluster** template to create the Dedicated FPGA Kubernetes cluster.|All|None.|
|New version of Cloud Controller Manager \(CCM\)|The CCM component is upgraded to v1.9.3.110-g4938309-aliyun, which supports more features of SLB configurations. This version contains the following new features: -   You can configure parameters to limit whether SLB instances located in the public network can be created.
-   You can change the certificate ID.
-   You can specify a VSwitch when attaching an SLB instance located in the internal network to a service.
-   You can configure to forward SLB traffic from HTTP over port 80 to HTTPS over port 443.

 |All|[Changes to Cloud Controller Manager](../../../../intl.en-US/Release notes/Changes to Cloud Controller Manager.md#)|
|Upgrading to Istio 1.1.4 to access Time Series and Spatial-Temporal Database \(TSDB\).| ACK Istio 1.1.4 improves self-recovery capability. In this version, the control panel can automatically recover, and earlier versions can automatically be upgraded. ACK Istio 1.1.4 also provides the ability to access TSDB. TSDB is a database service that integrates efficient read/write, compressed storage, and real-time computing capabilities of sorted data. To address the pain points of Prometheus local storage, you can integrate Prometheus with ACK Istio 1.1.4 and use a remote storage adapter to connect a TSDB instance. This type of instances offers remote storage services with high performance, stability, and reliability at low costs.

 Compared with open-source remote storage solutions, this solution is easier to use. You only need to change the Prometheus configuration because it has high integration with ACK Istio and does not require installation and deployment of adapters. This solution supports parallel reads and writes and is highly compatible with Prometheus Query Language \(PromQL\). It also offers the auto scaling capability of distributed storage systems.

 |All|None.|
|Adding support to synchronize images across all the regions|The feature of synchronizing images across all the supported regions is available in Container Registry \(ACR\) Enterprise Edition. This feature supports automatic image synchronization across different regions. This solves the pain points in the delivery of globalization container application and helps enterprises improve the efficiency of business innovation and iteration. Container Registry Enterprise Edition offers the capabilities of enhanced security protection and large-scale image distribution. It is suitable for enterprise-level customers with high-security requirements and large-scale nodes.|All|None.|
|Multi-zone configuration and 5-master node configuration|Starting from May 2019, ACK allows you to set multiple zones when creating a Kubernetes cluster and set five master nodes for a dedicated Kubernetes cluster. This improves the availability of the Kubernetes clusters.|All|None.|

## April 2019 {#section_mjp_zae_4p2 .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|Support for upgrading to Kubernetes v1.12.6|Kubernetes v1.12.6 is available in all regions. If you are using the Dedicated Kubernetes clusters and Managed Kubernetes clusters of the version v1.11.5, you can upgrade the cluster version to v1.12.6 in the console.|All|None.|
|Adding support for audit logs in Managed Kubernetes clusters|Collecting audit logs is supported in Managed Kubernetes. Audit logs for API servers can help cluster administrators to record or trace the daily operations of different users.|All|[Kube-apiserver audit logs](../../../../intl.en-US/User Guide for Kubernetes Clusters/Security management/Kube-apiserver audit logs.md#)|
|Adding support to manage applications in the management console through Istio v1.1.| ACK Istio is upgraded to v1.1. You can manage applications in Kubernetes clusters in which the Istio v1.1 is deployed. You can use the graphic user interface \(GUI\) to create and manage Istio applications and services. In different phases, you can create versions for applications and set various policies. You can also configure fault injection polices to test the resilient ability of your services. In the management console, choose **Service Mesh** \> **Virtual Service** to configure these settings.

 |All|[EN-US\_TP\_151931.md\#](intl.en-US/User Guide for Kubernetes Clusters/Istio management/Manage traffic.md#)|
|Adding support to create GPU container instances in Serverless Kubernetes clusters|You can create applications based on GPU container instances in a Serverless Kubernetes cluster. You can create applications by selecting a template and specifying the pod type as GPU in the YAML file.|All|[Use a GPU container instance](../../../../intl.en-US/Best Practices/Serverless/Use a GPU container instance.md#)|
|More available regions for Container Registry Enterprise Edition|A region has been added to support Container Registry Enterprise Edition. Starting from April 2019, you can use Container Registry Enterprise Edition in China North 2 \(Beijing\).|China \(Beijing\)| |
|Adding support for image and video encoding acceleration in FPGA clusters|Container Service has launched FPGA clusters, which use FPGA F3 instances as worker nodes. An FPGA cluster is suitable to perform video and image computing tasks, such as encoding videos with the H.265 standard and converting JPEG images to HEIF images. Compared with previous FPGA solutions of video encoding acceleration that spends one week, using the Dedicated FPGA Kubernetes clusters can reduce the time to a short time. This significantly saves the CDN bandwidth for the bitrate of transcoding and optimization of non-compression video quality. In the management console, choose **Clusters** \> **Clusters**, and click Create Kubernetes Cluster. On the Select Cluster Template page, select the **Dedicated FPGA Cluster** template to create the Dedicated FPGA Kubernetes cluster.|All|None.|

## March 2019 {#section_rio_r10_k4q .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for Managed Kubernetes|More regions have been added to support Managed Kubernetes. Starting from March 2019, you can use Managed Kubernetes in China \(Zhangjiakou-Beijing Winter Olympics\), China \(Hohhot\), US \(Silicon Valley\), and Germany \(Frankfurt\).| China \(Zhangjiakou-Beijing Winter Olympics\)

 China \(Hohhot\)

 Germany \(Frankfurt\)

 US \(Silicon Valley\)

 |[EN-US\_TP\_15496.md\#](intl.en-US/Product Introduction/Alibaba Cloud Container Service for Kubernetes Overview.md#)|
|Container Registry Enterprise Edition|Container Registry Enterprise Edition was officially released at the Alibaba Cloud Summit on March 21, 2019. This edition offers high security and image distribution capabilities. This edition is in the beta phase and only available in China \(Shanghai\). Open a ticket if you want to use it.|China \(Shanghai\)|[What is Container Registry?](https://www.alibabacloud.com/help/zh/doc-detail/60945.html)|
|More available regions for Container Registry Shared Edition in the International site \(alibabacloud.com\)|Container Registry Shared Edition is available in all regions on the International site \(alibabacloud.com\).|All|[What is Container Registry?](https://www.alibabacloud.com/help/zh/doc-detail/60945.html)|
|Support for upgrading to Kubernetes v1.12.6|The Kubernetes version of the ACK is upgraded to 1.12.6. You can create Kubernetes clusters of version 1.12 through the console.|All|[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|
|Support for the Log Service plug-in in Managed Kubernetes clusters|Log Service plug-in is supported in Managed Kubernetes clusters. You can choose to use Log Service when creating a cluster in Managed Kubernetes. This plug-in enables the strong management capabilities of Log Service to manage Kubernetes logs.|All|[Create a managed Kubernetes cluster](../../../../intl.en-US//Create a managed Kubernetes cluster.md#)|
|Adding support to create Windows clusters for Managed Kubernetes|Managed Kubernetes supports to create Windows containers in the Kubernetes clusters. You can create clusters and deploy Windows containers through the console or APIs, allowing traditional Windows applications to integrate the agility and elasticity provided by cloud-native platforms.|All|[../../../../dita-oss-bucket/SP\_235/DNcsk1877477/EN-US\_TP\_149635.md\#](../../../../intl.en-US//Create a Kubernetes cluster that supports Windows.md#)|
|Adding support for IPVs|The IPVS proxy mode that is different from the traditional IPTables mode has been added to ACK. The IPVS proxy mode significantly improves the load balancing performance in large-scale Kubernetes clusters. You can use this mode in all clusters of all regions.|All|[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|
|Cluster templates|The ACK console provides the new feature of cluster templates. Based on business scenarios, you can select different cluster templates to create various Kubernetes clusters, such as Managed Kubernetes clusters, Elastic Bare Metal clusters, GPU clusters, and Windows clusters. By using cluster templates, you can easily create customized Kubernetes clusters.|All|None.|
|Expanding ECI specifications supported by Serverless Kubernetes clusters|The Serverless Kubernetes cluster expands ECI instance specifications \(from 8 vCPUs to 64 vCPUs\), which can support genomics computing scenarios. The maximum specification is 64 vCPUs and 256 GiB memory, and the minimum specification is 0.25 vCPUs and 0.5 GiB memory. A wide range of instance types allow you to have more options when deploying services to achieve the best performance ratio.|All|[Limits](https://www.alibabacloud.com/help/zh/doc-detail/89138.html)|

## February 2019 {#section_yce_ixg_62w .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for Managed Kubernetes|Managed Kubernetes provides the following core benefits: -   Saves resources. Managed Kubernetes can save three master nodes for each cluster.
-   Simplifies operations and maintenance \(O&M\). ACK helps manage master nodes.
-   High security. ACK offers high security of your data to meet your requirements.

 |China \(Shenzhen\)|[Create a managed Kubernetes cluster](../../../../intl.en-US//Create a managed Kubernetes cluster.md#)|
|Knative add-on| Knative is a scale-to-zero and request-driven runtime environment based on Kubernetes and Istio for computing. It provides deployment and service features for serverless applications and functions.

 ACK released the Knative add-on to help you quickly build the Knative Serving environment based on the ACK clusters

 |All|[Knative overview](../../../../intl.en-US/User Guide for Kubernetes Clusters/Knative management/Knative overview.md#)|
|Intelligent cluster O&M features|Intelligent cluster O&M features can help you quickly locate cluster usage problems by performing in-depth checks on cluster resources, components, and configurations.|Mainland China|[Check a Kubernetes cluster to troubleshoot exceptions](../../../../intl.en-US/Best Practices/Operation and maintenance/Check a Kubernetes cluster to troubleshoot exceptions.md#)|

## January 2019 {#section_wwx_s33_u6g .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|Beta version of Windows containers| ACK supports to create Windows containers in the Kubernetes clusters. Applications in Windows can also run on Kubernetes in a containerized manner, which integrates the agility and elasticity provided by Kubernetes.

 You can add Windows containers in the Standard Kubernetes clusters and the Managed Kubernetes clusters by adding nodes.

 The Windows container feature is in the beta phase. Open a ticket if you want to use it.

 |All|[../../../../dita-oss-bucket/SP\_235/DNcsk1877477/EN-US\_TP\_149635.md\#](../../../../intl.en-US//Create a Kubernetes cluster that supports Windows.md#)|
|Beta version of Container Registry Enterprise Edition|The Container Registry Enterprise Edition has implemented a complete set of container image repository built on independent resources. The container image repository provides secure management of enterprise-level images, strong ability of large-scale image distribution, and stable image building service. Container Registry Enterprise Edition is suitable for enterprise-level customers with large-scale cluster nodes and high-security requirements. The Container Registry Enterprise Edition is in the beta phase. Open a ticket if you want to use it.

 |All|[Container Registry Enterprise Edition](https://help.aliyun.com/document_detail/111958.html)|
|More regions available for the intelligent cluster O&M feature|The intelligent cluster O&M is designed to provide you with the best practice for container clusters in different scenarios. You can quickly locate cluster usage problems by performing in-depth checks on cluster resources, components, and configurations.|China \(Hangzhou\)|[Check a Kubernetes cluster to troubleshoot exceptions](../../../../intl.en-US/Best Practices/Operation and maintenance/Check a Kubernetes cluster to troubleshoot exceptions.md#)|
|Application Real-Time Monitoring Service \(ARMS\) compatibility|Container service is integrated with the ARMS. After you install the corresponding ARMS plug-ins in the cluster, you can monitor the performance of the applications that are deployed in the cluster. ARMS is a performance management \(APM\) software for Java applications. You only need to attach a probe to the startup script of the Java application without changing any code. This probe can fully monitor your Java application. For example, you can quickly locate the operations that are failed or slow to call, reproduce parameters, detect memory leaks, and discover system bottlenecks. This improves the efficiency of online diagnosis.

 |All|None.|
|Starting to incur fees when using Elastic Container Instance \(ECI\) in Serverless Kubernetes|Starting from January 22, 2019, you need to pay the fees of ECI container instances that are scheduled by the underlying layer of Serverless Kubernetes. If you create ECI container instances in the Serverless Kubernetes clusters, the fees of ECI container instances are incurred. However, no fee is incurred for using Serverless Kubernetes itself.|All|[Billing methods](https://www.alibabacloud.com/help/zh/doc-detail/89142.html)|
|More available regions for Serverless Kubernetes|More regions have been added to support Serverless Kubernetes. You can deploy the Serverless Kubernetes clusters in China \(Beijing\) and China \(Shenzhen\) to obtain the benefits of Serverless Kubernetes.| China \(Beijing\)

 China \(Shenzhen\)

 |[EN-US\_TP\_16481.md\#](intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Create a serverless Kubernetes cluster.md#)|

## December 2018 {#section_3kc_5nz_vio .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for ACK|More regions have been added to support ACK. You can deploy ACK in all regions of the China site \(aliyun.com\) and in UK \(London\) of the International site \(alibabacloud.com\) to integrate the cloud-native capabilities of Kubernetes containers.|UK \(London\)|[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|
|More available regions for Managed Kubernetes|More regions have been added to support Managed Kubernetes. You can deploy Managed Kubernetes in all regions of the China site \(aliyun.com\) and in China \(Shanghai\), Malaysia \(Kuala Lumpur\), and India \(Mumbai\) of the International site \(alibabacloud.com\).| China \(Shanghai\)

 Malaysia \(Kuala Lumpur\)

 India \(Mumbai\)

 |[Create a managed Kubernetes cluster](../../../../intl.en-US//Create a managed Kubernetes cluster.md#)|
|Adding support to remove nodes in a cluster|You can remove a specified node from the cluster and choose whether to release the ECS instance or not.|All|[Remove a node](../../../../intl.en-US/User Guide for Kubernetes Clusters/Node management/Remove a node.md#)|
|Adding support to deploy DaemonSet applications|You can create a DaemonSet application. DaemonSet is a daemon process that has only one Pod on each node of the cluster.|All|None.|
|Support for custom Istio gateways|You can customize the Istio ingress and egress gateways by configuring different parameters.|All|None.|
|Support for Istio CoreDNS|Implemented DNS addressing of Istio Service Entry based on CoreDNS Plugin extension.|All|None.|
|Adding support to create a Managed Kubernetes cluster by selecting existing ECS instances|You can select an existing ECS node when creating a Managed Kubernetes cluster rather than creating a new node.|All|[Create a managed Kubernetes cluster](../../../../intl.en-US//Create a managed Kubernetes cluster.md#)|

## November 2018 {#section_0oc_qug_h23 .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for Managed Kubernetes|More regions have been added to support Managed Kubernetes. You can deploy Managed Kubernetes in Indonesia \(Jakarta\) of the International site \(alibabacloud.com\) to integrate the cloud-native capabilities of Kubernetes containers.|Indonesia \(Jakarta\)|[Create a managed Kubernetes cluster](../../../../intl.en-US//Create a managed Kubernetes cluster.md#)|
|Terway plug-in|Alibaba Cloud Container Service introduces the Terway plug-in, which is a high-performance network plug-in. This plug-in supports direct communications between containers through ENI with higher performance than VPC Flannel.|All|[Terway network plugin](../../../../intl.en-US/User Guide for Kubernetes Clusters/Network management/Terway network plugin.md#)|
|Displaying performance metrics of Worker nodes through thumbnails|A feature that allows you to display the thumbnail of performance metrics for each Worker node has been added. You can use this feature to view the node status.|All|None.|
|Support for adding multiple existing nodes at a time|You can add multiple existing nodes in a cluster or select multiple nodes to join a Kubernetes cluster at a time.|All|None.|
|Certificate rolling|The cluster supports certificate rolling to avoid certificate expiration.|All|None.|

## October 2018 {#section_uct_q80_y2s .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for ACK on Finance Cloud|You can deploy ACK on the Finance Cloud in China \(Shenzhen\) and obtain the security and compliance guarantees of Finance Cloud.|China \(Shenzhen\)|[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|
|More available regions for Managed Kubernetes|None.|Outside China|[Create a managed Kubernetes cluster](../../../../intl.en-US//Create a managed Kubernetes cluster.md#)|
|Deployment version management and rollback|ACK has optimized application management to support the version management and rollback of Deployment.|All|None.|
|In-depth integration between Istio add-on and Kubernetes|Supports in-depth integration between Istio and container service Kubernetes clusters.|All|[Overview](../../../../intl.en-US/User Guide for Kubernetes Clusters/Istio management/Overview.md#)|

## September 2018 {#section_iwl_aq8_bim .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|Release of ACK v1.11| -   Provides new features including CRD upgrade, CoreDNS GA, pod priority setting, and preemptive scheduling.
-   Supports multiple versions when creating a Kubernetes instance in the cluster. You can select Kubernetes v1.10 or v1.11 as needed.
-   The console supports multiple containers and stateful applications.

 |All|[Create a StatefulSet application by using an image](../../../../intl.en-US/User Guide for Kubernetes Clusters/Application management/Create a StatefulSet application by using an image.md#)|
|Pulling images from private image repositories of Alibaba Cloud Container Registry without using passwords|ACK allows you to pull images from private repositories of Alibaba Cloud Container Registry without using passwords.|All| |
|Automatic node scaling|Alibaba Cloud Container Service provides the auto scaling feature through auto scaling nodes. Standard instances, GPU instances, and preemptible instances can be automatically added to or remove from the scaling group according to your configurations. This feature is applicable to various scenarios such as instances across multiple regions, diverse instance types, and different scaling modes.|All|[Autoscale the nodes of a Kubernetes cluster](../../../../intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Autoscale the nodes of a Kubernetes cluster.md#)|
|Support for preemptible instances|None.|All| |

## August 2018 {#section_qul_swa_zp8 .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|Beta version of Managed Kubernetes|Managed Kubernetes is in the beta phase and available for all regions.|All|[Create a managed Kubernetes cluster](../../../../intl.en-US//Create a managed Kubernetes cluster.md#)|
|Istio add-on|The Istio add-on has been released.|All|[Overview](../../../../intl.en-US/User Guide for Kubernetes Clusters/Istio management/Overview.md#)|

## July 2018 {#section_t5h_01k_vav .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for ACK|None.|Australia \(Sydney\)|[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|
|Adding support for canary release and phased release|None.|All|[Gray releases and blue/green deployment](../../../../intl.en-US/Best Practices/Release/Implement a gray release and a blue__green deployment through Ingress in a Kubernetes cluster/Gray releases and blue__green deployment.md#),[Use batch release on Alibaba Cloud Container Service for Kubernetes](../../../../intl.en-US/User Guide for Kubernetes Clusters/Release management/Use batch release on Alibaba Cloud Container Service for Kubernetes.md#)|

## June 2018 {#section_e00_ca3_51q .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for ACK|None.| Japan \(Tokyo\)

 China \(Hohhot\)

 |[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|
|FPGA and HugePages features|None.|All|None.|
|Application monitoring and alerts|Monitoring and alerts for applications have been added to improve the monitoring ability of ACK.|All|[Integration and usage with CloudMonitor](../../../../intl.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress management/Integration and usage with CloudMonitor.md#)|
|Adding support to create subscription-based clusters for ACK|None.|All|[Create a Kubernetes cluster](../../../../dita-oss-bucket/SP_235/DNcsk1877477/intl.en-US//Create a Kubernetes cluster.md#)|
|Adding support for two Kubernetes commands\(kubectl attach and kubectl exec\) and Kubernetes ingress for Container Service for Serverless Kubernetes|None.|All|[Kubernetes supported functions](../../../../intl.en-US/User Guide for Serverless Kubernetes Clusters/Kubernetes supported functions.md#)|

## May 2018 {#section_qrs_14g_e92 .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for ACK on Finance Cloud|You can deploy ACK on the Finance Cloud in China \(Shanghai\) and obtain the security and compliance guarantees of Finance Cloud.|China \(Shanghai\)|[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|
|Release of Container Service for Serverless Kubernetes|None.|All|[Create a serverless Kubernetes cluster](../../../../dita-oss-bucket/SP_235/DNcsk1877477/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Create a serverless Kubernetes cluster.md#)|
|ACK supports blue-green release, canary release, and A/B testing.|None.|All|[Gray releases and blue/green deployment](../../../../intl.en-US/Best Practices/Release/Implement a gray release and a blue__green deployment through Ingress in a Kubernetes cluster/Gray releases and blue__green deployment.md#)|

## April 2018 {#section_bfo_2ei_scw .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|More available regions for ACK|ACK has been launched in all the five regions in Southeast Asia, the Middle East, and India. The latest and stable version 1.9 is available.| Malaysia \(Kuala Lumpur\)

 Indonesia \(Jakarta\)

 Singapore

 India \(Mumbai\)

 UAE \(Dubai\)

 |[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|
|Multi-zone Kubernetes clusters for high-availability scenarios|You can deploy your master nodes across three zones and deploy your worker nodes across multiple zones as needed to maximize the availability of cluster production.|All|[Create a multi-zone Kubernetes cluster](../../../../intl.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress management/Create a multi-zone Kubernetes cluster.md#)|
|Adding support for MySQL, RDS, RabbitMQ, and Spark for the service catalog|The service catalog for ACK has been updated to support MySQL, RDS, RabbitMQ, and Spark.|All|[Overview](../../../../intl.en-US/User Guide for Kubernetes Clusters/Service catalog management/Overview.md#)|
|Adding support for managing applications released by using Helm|The application catalog for ACK has been updated to support managing applications released by using Helm.|All|[Manage a Heml-based release](../../../../intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage a Helm-based release.md#)|

## March 2018 {#section_ld4_3by_nqq .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|Support for Kubernetes v1.9 and custom ECS images.|Container Service supports native Kubernetes v1.9.3 and the Workloads API is officially released. Custom Resource Definitions \(CRD\) is enabled by default and GPU scheduling is supported. You can select a custom ECS image when creating a cluster. You can automatically reset an image when adding a node.|All|None.|
|Application catalog|Application catalog has been released for ACK. You can use this catalog to implement one-click application deployments using Helm.|All|[Manage a Heml-based release](../../../../intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage a Helm-based release.md#)|
|Service catalog|Service catalog has been released for ACK. This catalog supports ServiceBroker.|All|[Overview](../../../../intl.en-US/User Guide for Kubernetes Clusters/Service catalog management/Overview.md#)|
|Node monitoring through CloudMonitor|Monitoring cluster nodes through CloudMonitor is available.|All|None.|

## January 2018 {#section_bj3_5y4_egq .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|Release of both Alibaba Cloud Container Registry and ACK on the International site \(alibabacloud.com\)|None.|Outside China|[Alibaba Cloud Container Service for Kubernetes Overview](../../../../dita-oss-bucket/SP_235/DNcsk1853535/intl.en-US/Product Introduction/Alibaba Cloud Container Service for Kubernetes Overview.md#)|
|Upgrading to Kubernetes v1.8.4, which provides security enhancement, auto scaling, and other features.|None.|All|[Autoscale the nodes of a Kubernetes cluster](../../../../intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Autoscale the nodes of a Kubernetes cluster.md#)|
|Release of FlexVolume that supports cloud disk, NAS, and OSS|The FlexVolume for ACK feature has been released to support cloud disks, NAS, and OSS.|All|[Use an Alibaba Cloud cloud disk as a persistent volume](../../../../intl.en-US//Use an Alibaba Cloud cloud disk as a persistent volume.md#),[Use an Alibaba Cloud NAS file as a persistent volume](../../../../intl.en-US//Use an Alibaba Cloud NAS file as a persistent volume.md#),[Use an Alibaba Cloud OSS bucket as a persistent volume](../../../../intl.en-US//Use an Alibaba Cloud OSS bucket as a persistent volume.md#)|
|Improved network policies and bandwidth limits|Network policies and bandwidth limits to improve network management in ACK have been released.|All|[Access services by using Server Load Balancer](../../../../intl.en-US/User Guide for Kubernetes Clusters/Network management/Access services by using Server Load Balancer.md#)|
|Support for ECS Bare Metal \(EBM\) instances|None.|All|None.|

## October 2017 {#section_tnz_5ho_cai .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|New native Kubernetes|The native Kubernetes version with the ACK v1.8.1 is supported.|All|[EN-US\_TP\_15496.md\#](intl.en-US/Product Introduction/Alibaba Cloud Container Service for Kubernetes Overview.md#)|
|Blockchain solution beta test|None.|All|[Overview](../../../../intl.en-US/solution/Blockchain solution/Overview.md#)|

## August 2017 {#section_4dn_hdv_znt .section}

|New feature|Description|Supported regions|References|
|-----------|-----------|-----------------|----------|
|Alibaba Cloud ACK v1.7.2|None.|All|[EN-US\_TP\_16639.md\#](intl.en-US//Create a Kubernetes cluster.md#)|


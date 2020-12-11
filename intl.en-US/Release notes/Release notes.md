# Release notes

This topic describes release notes of Alibaba Cloud Container Service for Kubernetes \(ACK\).

-   ACK supports Kubernetes V1.16.9, V1.14.8, and V1.12.6. V1.12.6 is available to only the users in the whitelist.

-   ACK supports the following operating systems: CentOS 7.7, Alibaba Cloud Linux 2.1903, and Windows Server 2019.

## September 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports ACK in the China \(Ulanqab\) region|You can deploy ACK clusters in the China \(Ulanqab\) region.|All regions

|[ACK Pro clusters](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/ACK Pro clusters.md)|
|Enables Server Message Block \(SMB\) file systems for Windows containers|ACK allows you to mount an SMB file system to Windows containers. In the NAS console, you can create an SMB file system in the VPC where the cluster is deployed, and create a mount point. Before you mount an SMB file system, configure the FlexVolume plug-in.|All regions

|[Mount SMB file systems to Windows containers](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Mount SMB file systems to Windows containers.md)|
|Supports specifying the time zone for a cluster|You can specify the time zone for master nodes and worker nodes when you create a dedicated or managed ACK cluster.|All regions

|None|
|Releases the official version of ACK V1.18|ACK V1.18.8 is officially released. You can select this version when you create a cluster.|All regions

|[Release notes for Kubernetes 1.18](/intl.en-US/Release notes/Kubernetes release notes/Release notes for Kubernetes 1.18.md)|
|Enables Terway to support network policies|You can specify whether to enable the network policy feature for Terway when you create a cluster.|All regions

|-   [Use network policies for access control](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use network policies for access control.md)
-   [Optimize the performance of the NetworkPolicy feature for a large ACK cluster in Terway mode](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Best practices/Optimize the performance of the NetworkPolicy feature for a large ACK cluster in Terway
         mode.md) |
|Enables the Security module to support periodic inspection of an ACK cluster|You can use the Security module to configure periodic inspection policies for an ACK cluster.|All regions

|[Use the inspection component to check for workload security risks in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the inspection component to check for workload security risks in an ACK cluster.md)|
|Enables the Security module to support cluster auditing|You can use the Security module to configure cluster auditing.|All regions

|[Use the cluster auditing feature](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the cluster auditing feature.md)|
|Adds components such as logtail-ds, migrate-controller, and ack-virtual-node to manage registered clusters|The logtail-ds component collects container logs from Kubernetes clusters, including stdout logs and text files of containers.

The migrate-controller component is developed based on the open source Velero project. You can use migrate-controller to migrate applications across Kubernetes clusters.

The ack-virtual-node component is used to enable auto scaling for registered Kubernetes clusters.

|All regions

|-   [Install and use logtail-ds](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install and use logtail-ds.md)
-   [Install migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install migrate-controller.md) |
|Upgrades Sandboxed-Container to V2.0|Sandboxed-Container is upgraded to V2.0. Sandboxed-Container V2.0 has the following benefits:-   Sandboxed-Container V2.0 is the container runtime that is developed by Alibaba Cloud based on lightweight virtual machines. Compared with Sandboxed-Container V1.0, Sandboxed-Container V2.0 supports more lightweight and efficient deployment and simplifies the architecture and maintenance of ACK clusters.
-   Sandboxed-Container V2.0 reduces the resource overheads by 90% and accelerates Sandboxed-Container startup by three times.
-   Sandboxed-Container V2.0 increases the deployment density of standalone sandboxed containers by 10 times.
-   Sandboxed-Container V2.0 supports the virtio-fs file system, which provides higher performance than the 9pfs file system.

|All regions

|[Sandboxed-Container overview](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md)|
|Supports Knative components for Alibaba Cloud Serverless Kubernetes \(ASK\) clusters|Knative is a cloud-native cross-platform orchestration engine for serverless applications. ASK allows you to deploy Knative on serverless clusters. In this case, you can use cloud resources by calling Knative API operations. You do not need to pay for the Knative controller.|All regions

|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Overview.md)|

## August 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports the Open Policy Agent \(OPA\) Gatekeeper add-on|You can install the OPA Gatekeeper add-on in the ACK console. This component helps you manage and enforce OPA policies in Kubernetes clusters.|All regions

|[Introduction](/intl.en-US/Release notes/System Component change Records/Release notes for gatekeeper/Introduction.md)|
|Enables the Security module to detect the runtime security|The Security module in the ACK console provides the Runtime Security feature. This feature monitors ACK clusters and triggers alerts if the following types of security events occur: malicious image startups, attacks by viruses or malware in containers and hosts, intrusions into containers, container escapes, and high-risk operations on containers. Before you enable Runtime Security, activate Security Center. If you log on as a RAM user, make sure that the RAM user has permissions to access Security Center.|All regions

|[Use Runtime Security to monitor ACK clusters and configure alerts](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use Runtime Security to monitor ACK clusters and configure alerts.md)|
|Supports microservice governance for ACK clusters|You can enable microservice governance in the ACK console. You can connect Dubbo and Spring Cloud microservices that are deployed in ACK clusters to Microservice Engine \(MSE\). MSE helps you improve the stability and development efficiency of online microservices.|All regions

|[t1884235.md\#]()|
|Supports scheduled backup of block storage devices|ACK allows you to create scheduled snapshots for disks. To use this feature, install theStorage Operator component.|All regions

|None|
|Enables Terway to support IPVLAN and extended Berkeley Packet Filter \(eBPF\)|Assume that you have configured the Terway network plug-in. If an elastic network interface \(ENI\) is shared among pods, you can use IPVLAN and eBPF for network virtualization.Terway enables pod network virtualization based on the lightweight IPVLAN technology. This allows pod traffic to bypass the network stack of hosts and reduces network performance overheads. Terway uses Cilium as the BPF agent on nodes to configure the BPF rules for pod ENIs. This enables services and network policies to be configured on ENIs, and the network requests within pods are forwarded to ENIs through IPVLAN. This reduces the network complexity.

**Note:** This feature is based on the Alibaba Cloud Linux 2 operating system. Users in the whitelist can use this feature. To use this feature, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

|All regions

|[Use Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md)|
|Supports professional managed clusters in the China \(Beijing\), China \(Shenzhen\), Germany \(Frankfurt\), Indonesia \(Jakarta\), and China East 2 Finance regions|You can deploy professional managed clusters in the China \(Beijing\), China \(Shenzhen\), Germany \(Frankfurt\), Indonesia \(Jakarta\), and China East 2 Finance regions.|China \(Beijing\), China \(Shenzhen\), Germany \(Frankfurt\), Indonesia \(Jakarta\), and China East 2 Finance|[ACK Pro clusters](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/ACK Pro clusters.md)|
|Releases the ACK@Edge service for commercial use|ACK provides the ACK@Edge service to achieve cloud-edge collaboration in edge computing scenarios.|All regions

|[t1936963.md\#]()|

## July 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Releases ACK Pro Edition for public preview|ACK Pro clusters are developed for large-scale business based on managed ACK clusters. ACK Pro clusters enhance the reliability and security. ACK Pro clusters also provide the service-level agreement \(SLA\) that supports compensation clauses. This cluster type is suitable for the following users:-   Internet enterprises that need to deploy workloads in large-scale production environments. These enterprises require high security, stability, and observability.
-   Big data computing enterprises that implement large-scale, high-performance, and high-elasticity data processing. These enterprises require high cluster stability and efficiency.
-   International enterprises that run business in China. These enterprises attach significant importance to security, privacy, and SLAs with indemnification clauses.
-   Financial enterprises that require SLAs with indemnification clauses.

|All regions

|[ACK Pro clusters](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/ACK Pro clusters.md)|
|Supports ASK clusters in the Japan \(Tokyo\) and Indonesia \(Jakarta\) regions|You can create ASK clusters in the Japan \(Tokyo\) and Indonesia \(Jakarta\) regions.|Japan \(Tokyo\) and Indonesia \(Jakarta\)|[Introduction](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Introduction.md)|
|Upgrades Cloud Controller Manager \(CCM\) to V1.9.3.313-g748f81e-aliyun|CCM provides the following new features:-   Supports deletion protection for Server Load Balancer \(SLB\) instances. By default, deletion protection is enabled for newly created SLB instances.
-   Supports the configuration read-only mode for SLB instances. By default, the configuration read-only mode is enabled for newly created SLB instances.
-   Supports specifying the resource group when you create an SLB instance.
-   Supports specifying an SLB instance name when you create the instance.
-   Automatically mounts pods to an SLB instance after you create the SLB instance. This applies to a cluster that uses the Terway plug-in.

|All regions

|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|Enables the Security module to support pod security policies \(PSPs\) and cluster inspection|You can use the Security module in the ACK console to configure PSPs and inspect clusters.PSPs are used to verify pod deployment. This ensures the runtime security of applications. The cluster inspection feature allows you to detect the security risks of workload settings in an ACK cluster and view inspection reports.

|All regions

|[Use a PSP](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use a PSP.md)|
|Supports VPC sharing in an ACK cluster|VPC sharing allows multiple accounts to create cloud resources in a VPC. The cloud resources include Elastic Compute Service \(ECS\) instances, SLB instances, and ApsaraDB for RDS instances. You can manage the VPC in a unified manner. This feature is based on the resource sharing mechanism. The Alibaba Cloud account that owns a VPC can share all vSwitches in the VPC with other accounts under the same organization. You can select a shared VPC when you create a cluster. If you select a shared VPC, you can use only Terway as the network plug-in.|All regions

|None|
|Supports cluster registration|During daily operations and maintenance \(O&M\), you may deploy multiple clusters on the cloud. You may also deploy clusters on the cloud and in your data center at the same time. In this case, you can register external clusters in the ACK console. This allows you to manage multiple clusters in the console and reduce O&M costs.|All regions

|[Overview of registered clusters in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview of registered clusters in the ACK console.md)|
|Supports redeployment and rollback of workloads|ACK provides new features on the workload management page, such as application redeployment and rollback. This helps you manage workloads.|All regions

|[Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md)|

## June 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports taint management in node pools|You can configure taints when you create or edit a node pool. This allows you to add taints to all nodes in the node pool. You can select **Synchronize Node Labels and Taints** to update taint information of existing nodes in the node pool.|All regions

|[Manage taints](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Manage taints.md)|
|Supports migrating applications on virtual machines to ACK clusters by using Server Migration Center \(SMC\)|SMC allows you to migrate servers to Container Registry. You can use SMC to migrate containerized applications to Container Registry at low costs.|All regions

|[Migrate source servers to Container Registry](/intl.en-US/Best Practices/Migrate source servers to Container Registry.md)|

## May 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports advanced security groups for ACK clusters|ACK provides multiple types of security groups when you create a cluster. You can select a basic security group, an advanced security group, or an existing security group. Compared with basic security groups, each advanced security group can contain up to 65,536 private IP addresses. Advanced security groups are used for clusters where a large number of containers or instances are deployed.|All regions|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports Prometheus and Event Center|ACK is integrated with the Prometheus component that is most commonly used to monitor containers, and the node-problem-detector component that is most commonly used for O&M. You can select the components when you create a cluster. You can also upgrade and maintain the components on the Components page. The Prometheus component is provided by Application Real-Time Monitoring Service \(ARMS\). The node-problem-detector component is used to detect node problems, such as Docker Engine exceptions, Linux kernel exceptions, network issues, and file descriptor issues. To view these problems, go to the management page of a cluster, and click **Events** in the left-side navigation pane. On the page that appears, click **Cluster Events Management**.|All regions|[ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/ARMS Prometheus.md)|
|Supports Kubernetes V1.16.9|ACK supports Kubernetes V1.16.9. You can create a cluster of Kubernetes V1.16.9. If your service version is earlier than V1.16.9, choose **More** \> **Upgrade Cluster**, and upgrade the service on the Upgrade Cluster page. Compared with the previous Kubernetes V1.16.6, Kubernetes V1.16.9 fixes the CVE-2020-8555 SSRF vulnerability of the kube-controller-manager component.|All regions|[Vulnerability fix: CVE-2020-8555](/intl.en-US/Bulletin/Security bulletins/Vulnerability fix: CVE-2020-8555.md)|
|Supports elastic workloads|ACK supports elastic workloads. On the **App Catalog** page, select **ack-kubernetes-elastic-workload** to install the component. You can use ACK and Virtual Kubelet in combination to schedule pay-as-you-go and preemptible instances by proportion. This enables elastic workload scheduling.|All regions|[View the application catalog](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/App catalog management/View the application catalog.md)|
|Provides the Application Center|In earlier versions, after applications are deployed, the topology of applications is not displayed in a unified view. Unified version management and rollback cannot be achieved for continuous deployment. Application Center provides a unified portal for your applications. This allows you to globally view the deployment of the applications. You can also view the deployment status and changes of all ACK sub-resources that is allocated to each application. Templates on GitHub and Helm charts are also used to deploy applications in ACK clusters based on versions. This allows you to release or roll back different versions of applications.|All regions|[Overview]()|

## April 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Releases Alibaba Cloud Genomics Service \(AGS\) for commercial use|AGS is an ACK-based big data computing service that is provided by Alibaba Cloud for users in the biological industry. AGS provides efficient, elastic, and reliable services. AGS is faster in computing and more cost-efficient than traditional methods. AGS uses the pay-as-you-go billing method based on the number of successful API calls in the backend. To submit a computing task, run a command on the client to call an API operation.|All regions|[Overview](/intl.en-US/User Guide for Genomics Service/Overview.md)|
|Supports online scale-out for dynamic volumes|For Kubernetes V1.16 and later, ACK supports volume scale-out without restarting pods.|All regions|[Use CSI to expand PVs online in ACK](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use CSI to expand PVs online in ACK.md)|
|Supports deployment of multiple Ingress controllers|An ingress is an important entry for Layer 7 services. If you create one Ingress for a cluster, the routing performance may encounter a bottleneck. If an Ingress allows inbound access through the Internet and internal networks at the same time, security risks exist. To solve these issues, ACK provides a Helm chart named ack-ingress-nginx for an Ingress controller. You can deploy multiple Ingress controllers in App Catalog. You can use YAML files to configure access to SLB instances through the Internet or internal networks.|All regions|[Deploy Ingresses in a high-reliability architecture](/intl.en-US/Best Practices/Network/Deploy a highly reliable Ingress controller.md)|
|Supports ASK clusters in the India \(Mumbai\) region|ASK is available in the India \(Mumbai\) region.|India \(Mumbai\)|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|

## March 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Adds features to component management|The following features are added to component management: -   Supports displaying the YAML files of components.
-   Supports health check for nodes before component upgrade. This prevents component upgrade failures that are caused by node drains or exceptions.
-   Supports refreshing the Components page.

|All regions|[Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Manage system components.md)|
|Enables CCM to add custom ECS instances to the backend of SLB instances|CCM allows you to add custom ECS instances to the backend of SLB instances. In this case, the existing applications and containerized applications share the same SLB instance and inbound traffic. This is applicable to the scenarios where existing applications are gradually replaced by containerized applications.|All regions|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|Enables Terway to support expanding clusters and changing node specifications|When you expand a cluster, you may need to create nodes in new zones. In earlier versions, to create pods in a new zone, you must first add new pod vSwitches in the new zone. You can add pod vSwitches in Terway ConfigMaps. When you change node specifications, the maximum number of pods that you can create in the node also changes. The K8s max-pod parameter is automatically adjusted to match the specifications of the node.|All regions|[Use Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md)|
|Supports node pool management|A node pool is a group of nodes with the same configurations. For example, nodes in a node pool are configured with the same container runtime, operating system \(OS\), and security group. You can create multiple node pools for a cluster. This allows you to deploy a variety of services to different node pools in a cluster. Node pools also support auto scaling. Nodes can be automatically added when a node pool is running with insufficient resources.|All regions|[Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md)|
|Optimizes the cluster check feature|Cluster check is the core feature of ACK maintenance. Cluster check dynamically scans clusters to identify potential risks. The optimized feature supports the following services: -   Displays information about unknown hosts.
-   Checks the availability of Yellow dogUpdater, Modified \(YUM\).
-   Checks the availability of systemd.

|All regions|[Use cluster check to troubleshoot cluster issues](/intl.en-US/Best Practices/Operation and maintenance/Use cluster check to troubleshoot cluster issues.md)|
|Supports Kubernetes V1.16|ACK allows you to create clusters of Kubernetes V1.16. You can also upgrade Kubernetes V1.14.8 to V1.16.6. Before you upgrade your clusters, we recommend that you refer to the upgrade notes.|All regions|[Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Upgrade a cluster.md)|
|Supports managed ACK clusters in the China South 1 Finance region on Alibaba Finance Cloud|Managed ACK clusters are available in the China South 1 Finance region on Alibaba Finance Cloud.|China South 1 Finance|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports configuring ephemeral-storage for containers|ephemeral-storage is a new resource mode that is similar to CPU and memory. You can use this parameter to manage and schedule the transient storage of applications that run in Kubernetes clusters. The root directory and the /var/log directory of kubelet are stored on the primary partition of a worker node. EmptyDir volumes, container logs, image layers, and the writable layers of containers are also stored on the primary partition. The ephemeral-storage parameter is used to manage the primary partition. When you create an application, you can set requests and limits to schedule and manage the storage resources that are allocated to the application.|All regions|[Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md)|

## February 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports Kubernetes V1.16 and Docker V19.03.5|ACK supports Kubernetes V1.16 and provides enhanced cloud-native capabilities. Compared with the earlier version, Kubernetes V1.16 accelerates pod creation, and improves affinity, stability, and observability. You can select Docker V19.03.5 when you create clusters. ACK accelerates image building and container startup based on Docker V19.03.5.|All regions|[Kubernetes 1.16 release notes](/intl.en-US/Release notes/Kubernetes release notes/Kubernetes 1.16 release notes.md)|
|Updates auto scaling|The auto scaling feature is updated to support the following items: the Alibaba Cloud Linux2 operating system, custom security groups, and GPU-accelerated preemptible instances. The first two items are available to only the users in the whitelist. If you are not in the whitelist, submit a ticket.|All regions|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Supports worker nodes that run on CentOS 7.7|ACK supports CentOS 7.7. You can specify the CentOS 7.7 operating system when you create worker nodes. CentOS 7.7 is automatically used when you expand clusters or enable auto scaling of clusters.|All regions|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|Upgrades Helm to V3 in App Catalog|ACK supports Helm V3. You can install Helm V3 in App Catalog. Compared with Helm V2, Helm V3 improves the security of role assignment, provides full compatibility with Kubernetes Role-Based Access Control \(RBAC\) in multi-tenant scenarios, and supports more useful hooks.|All regions|For information about how to upgrade Helm from V2, see [Helm v2 upgrade notice](/intl.en-US/Bulletin/Helm v2 upgrade notice.md).|
|Supports ASK clusters in the Indonesia \(Jakarta\) and UK \(London\) regions|ASK clusters are available in the Indonesia \(Jakarta\) and UK \(London\) regions. You can create ASK clusters in these regions by using the console.|Indonesia \(Jakarta\) and UK \(London\)|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|Supports creating ClusterIP services for ASK clusters|ASK supports more options to deploy containerized applications. You can create ClusterIP services in ASK clusters. This enables access to your workload from within the cluster.|All regions|[Create a service](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md)|
|Enables CCM to attach ECS instances and ECIs to the backend servers of SLB instances|CCM allows you to attach ECS instances and ECIs to the backend servers of SLB instances that are associated with application services. This enables unified scheduling of pod resources across worker nodes and virtual nodes, and improves application resilience.|All regions|[Changes of Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|Supports 32-bit and 64-bit ARM nodes for edge clusters|Edge clusters support more heterogeneous infrastructures. Edge clusters allow you to add 32-bit and 64-bit ARM nodes. You can add Edge Node Service \(ENS\) nodes or nodes in data centers to edge clusters.|All regions|[Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md)|

## January 2020

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports ECI pods that are deployed on virtual nodes to access ClusterIP services|ACK allows ECI pods that are deployed on virtual nodes to access ClusterIP services. This enables Kubernetes to centrally manage virtual nodes and ECIs. You can deploy applications on virtual nodes without the trouble of resource capacity planning. This meets the requirements of scenarios such as online workload scaling, offline computing, and CI/CD, and reduces the overall computing costs. To enable this feature, log on to the console, choose App Catalog, find and install the ack-virtual-node plug-in.|All regions|[Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md)|
|Supports the ServiceAccountTokenVolumeProjection option on the Kubernetes API server|ACK allows you to enable the ServiceAccountTokenVolumeProjection option on the API server when you create a cluster. You can authenticate service accounts on pods. This option is also required if mutual Transport Layer Security \(TLS\) authentication is enabled on Istio through Secret Discovery Service \(SDS\).|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports more features for the CSI plug-in|You can select the CSI plug-in for storage when you create a cluster. The optimized CSI plug-in provides the following features: -   Supports mounting OSS subdirectories to containers.
-   Supports emptyDir volumes of the Memory type. A volume of the Memory type is a RAM-based temporary file system, whose storage space is limited by memory. This type of file system offers excellent performance and is used to provide cache space in containers.
-   Supports accelerated OSSFS transmission. OSSFS allows you to share data by mounting OSS buckets to local file systems in Linux. To meet the needs of big data and AI scenarios, ACK improves read speed by adjusting concurrency, block size, and libfuse configurations. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver).

|All regions|[Install CSI plug-ins](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Install the plug-in.md)|
|Improves the storage capabilities of sandboxed containers|Sandboxed containers enhance cloud-native capabilities and support disks and Network Attached Storage \(NAS\) file systems. This allows ACK to provide the same storage performance as virtual machines. ACK also supports rootfs blkio limits and disk I/O throttling on pods to meet the requirements in multi-tenant scenarios.|All regions|[Create an ACK cluster that supports sandboxed containers](/intl.en-US/API Reference/Clusters/Create a cluster/Create an ACK cluster that supports sandboxed containers.md)|
|Releases confidential computing clusters for public preview|Intel Software Guard Extensions \(SGX\)-based confidential computing clusters are used to protect sensitive data. The scenarios include smart contracts in blockchains, user secrets processing, intellectual property protection, genomics computing in bioinformatics, and edge computing. You can create confidential computing clusters, expand clusters, enable auto scaling, and add different types of nodes to the clusters. For more information, see [Create a managed Kubernetes cluster that supports confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster that supports confidential computing.md) and [SGX application development guide](https://developer.aliyun.com/article/740793). ACK also provides open source plug-in sgx-device-plugin to help you deploy SGX applications on Kubernetes clusters. For more information, see [Kubernetes device plugin for Intel SGX](https://github.com/AliyunContainerService/sgx-device-plugin). **Note:** Intel SGX is a set of CPU instruction code developed by Intel. Intel SGX allows developers to run application code and data in an enclave or Trusted Execution Environment \(TEE\), which is built based on hardware silos and the memory encryption technology. No application, OS Kernel, BIOS, or hardware other than the CPU can access an enclave without verification. All data in enclave memory is encrypted. Users encrypt the code and data in an enclave with their private keys that are obtained from Intel. An enclave can only be started after the signature is verified by using Intel Attestation Service \(IAS\).

|All regions|[Create a managed Kubernetes cluster that supports confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster that supports confidential computing.md)|
|Support calling API operations to perform gene sequencing tasks|ACK provides a set of API operations for genomics computing. You can call these API operations to submit gene sequencing tasks. Results are automatically uploaded to your OSS buckets. You do not need to manually create clusters and deploy tasks. The API operations support different SLAs and provide computing resources based on actual needs. This allows you to reduce costs and improve efficiency. This feature is in public preview. To use the feature, submit a ticket.|All regions|[Use AGS to perform WGS tasks](/intl.en-US/User Guide for Genomics Service/AGS acceleration API/Use AGS to perform WGS tasks.md)|

## December 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports component management for ACK clusters|ACK allows you to manage cluster components. You can log on to the ACK console. On the **Clusters** page, find the cluster, choose **More** \> **Manage System Components** on the right side of the page to manage cluster components. You can manage all system components and optional components by performing operations such as upgrading, uninstalling, and reinstalling components. More custom configurations will be available soon.|All regions|[Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Manage system components.md)|
|Supports ack-node-local-dns in App Catalog to accelerate DNS queries|App Catalog supports node local DNS, which sends internal DNS queries to CoreDNS and directly forwards external DNS queries to external DNS resolvers. Node local DNS caches all queries and provides DNS caching on each node. This significantly improves the overall DNS QPS of the cluster.|All regions|[View the application catalog](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/App catalog management/View the application catalog.md)|
|Supports managed ACK clusters in the China East 1 Finance region on Alibaba Finance Cloud|You can create managed ACK clusters in the China East 1 Finance region on Alibaba Finance Cloud. You only need to create worker nodes in managed clusters. ACK creates and manages master nodes. This cluster type is easy to use and provides high availability at low costs. You do not need to manage master nodes and therefore can focus on business development.|China East 1 Finance|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports ECS instances that have neural processing unit \(NPU\) capabilities|You can select ECS instances that have NPU capabilities when you create managed or dedicated ACK clusters. The instance type is ecs.ebman1.26xlarge, which is applicable to big data analytics and AI scenarios in video and graphics industries.|All regions|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Enhances the user experience of Terway|After you select the Terway plug-in, you may want to know the number of pods that are supported by different ECS instance types. Terway provides information about the number of pods that are supported by each ECS instance type when you create a cluster. When you expand a cluster, Terway also provides multiple options. This allows you to select vSwitches for nodes and pods. The user interface is optimized to provide clear instructions and accurate information.|All regions|[Use Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md)|

## November 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports selecting multiple zones and disks when you expand a cluster|The user interface for expanding a cluster is updated to provide the same configuration options as those for creating a cluster. You can select multiple zones when you expand a cluster. You can also mount multiple disks to a node and choose to encrypt these disks.|All regions|[Expand a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Expand a cluster.md)|
|Supports custom scripts, tags, and Operation Orchestration Service \(OOS\)|You can write data scripts to configure nodes when you create or expand a cluster. To use this feature, submit a ticket. You can use this feature to specify operating systems for your nodes. Instead of building custom images, you can inject scripts into standard images. Auto Scaling allows you to add tags to ECS instances. This helps you split bills based on the features of ECS instances. ACK is integrated with OOS. You can run OOS scripts to maintain nodes after you set nodes to unschedulable on the Nodes page.|All regions|[Expand a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Expand a cluster.md)|
|Supports multiple zones and log auditing for ASK clusters|After ASK is upgraded to version 2.0, ASK clusters provide more cloud-native features. Multiple zones and the log auditing feature are available. You can deploy pods across zones to improve the availability of your business. Log auditing improves the security of ASK clusters. ASK clusters will provide the same features as dedicated and managed ACK clusters.|All regions|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|Supports vGPU resources|To meet the needs of AI and big data applications, ACK supports vGPU resources. You can select instance types of the vgn5i instance family to create a cluster.|All regions|[Use GPU-based ECIs](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Serverless cluster best practices/Use GPU-based ECIs.md)|
|Enables Terway to support ENI buffer pools|Terway is a container network plug-in that is developed based on Alibaba Cloud ENI. The update enables Terway to create a buffer pool of ENI IP addresses during node initialization. This accelerates pod creation and improves user experience.|All regions|[Use Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md)|
|Enables CCM to add external ECS instances to the backend of SLB instances|CCM is a system component that is developed to mount services to the backend of SLB instances. By default, cluster nodes that host services are all mounted to the backend of the SLB instances. The update allows you to add ECS instances outside the cluster as backend servers to the SLB instances. This helps you perform application migration and phased releases.|All regions|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|

## October 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports Alibaba Cloud Linux 2|Alibaba Cloud Linux 2 is the latest operating system developed by Alibaba Cloud based on an advanced CentOS kernel version. Alibaba Cloud Linux 2.1903 is fully adapted to ACK. The operating system enables fast startup, provides optimized performance, and improves the efficiency and reliability of the cluster.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Provides the ingress dashboard feature in the ACK console|In earlier versions, you must manually configure the ingress dashboard, which is a time-consuming and error-prone task. A check box is added to the ingress configuration page. You only need to select the check box to enable the ingress dashboard feature. The ingress dashboard is automatically installed after the cluster is created.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports selecting SLB instance types|In earlier versions, when you create a service of the SLB type, ACK creates shared-performance SLB instances by default. To meet your needs in various scenarios, ACK allows you to select SLB instance types when you create a service. The SLB instances adopt the pay-as-you-go billing method.|All regions|[Create a service](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md)|
|Supports binding an elastic IP address \(EIP\) to the API server|SLB instances provide access to the API server in a cluster. When you create a cluster, ACK allows you to specify an internal or public SLB instance to handle traffic to the cluster. However, you may want to change the network settings of the SLB instance after the cluster is created. ACK allows you to bind an EIP to the SLB instance after the cluster is created. You can bind or unbind the EIP on the cluster details page. This allows you to change the network settings of the API server based on your needs.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports auto scaling of Edge Node Service \(ENS\) nodes in an edge cluster|To meet the needs of different scenarios, ACK allows you to configure automatic scaling for ENS nodes in the cluster. This feature can be implemented by calling an API operation.|All regions|[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|Supports more regions for ASK clusters|ASK clusters are available in the China \(Zhangjiakou\) region.|China \(Zhangjiakou\)|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|

## September 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions for ACK clusters|ACK clusters are available in the China \(Chengdu\) region. You can create dedicated ACK clusters. To create managed ACK clusters, submit a ticket.

|China \(Chengdu\)|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Upgrades from ACK V1.14.6 to ACK V2.0 and releases new upgrade features|ACK V1.14.6 releases new upgrade features that are available in the China \(Shanghai\), China \(Zhangjiakou\), Singapore \(Singapore\), and Germany \(Frankfurt\) regions. The features will soon be available in all regions. ACK also provides new features to simplify the upgrade process. You can click **Upgrade Cluster** on the Clusters page to perform the upgrade. ACK V2.0 provides the following features to improve upgrade security:

-   A comprehensive cluster check is performed before the upgrade.
-   You can manually pause or resume the upgrade process.
-   Detailed logs of the upgrade are recorded.

|-   China \(Shanghai\)
-   China \(Zhangjiakou\)
-   Singapore \(Singapore\)
-   Germany \(Frankfurt\)

|[Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Upgrade a cluster.md)|
|Supports node maintenance|To maintain nodes in a cluster, you must make sure that pods are not scheduled to the nodes. ACK supports node maintenance. You can select one or more nodes and set them to unschedulable on the Nodes page. You can also drain the nodes.

-   If you set a node to unschedulable, no new pods are scheduled to the node.
-   If you drain a node, existing pods on the node are migrated to other nodes and no new pods are scheduled to the node. However, pods that are managed by DaemonSets are not evicted from the node.

If you have a service of Server Load Balancer type and the pods that run the service are deployed on a node, you can configure the service to remove the node from the backend of the SLB instance when the node is set to unschedulable. This allows you to flexibly manage your nodes and workloads.

|All regions|[Mark a node as unschedulable](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Mark a node as unschedulable.md)|
|Supports custom node names|To manage a cluster that includes a large number of nodes, you must identify nodes by name. ACK allows you to customize node names when you create a cluster. On the Create Kubernetes Cluster page, you can click Show Advanced Options and customize node names in the **Custom Node Name** field. You can define a prefix, an IP substring length, and a suffix. The IP substring length specifies the number of digits at the end of the node IP address that is returned and can be used to uniquely identify a node.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports advanced security groups|Compared with basic security groups, advanced security groups support more ECS instances, more ENIs, and effective management of private IP addresses. Advanced security groups are suitable for users who have high requirements for the O&M efficiency, ECS instance specifications, and the number of compute nodes. To meet the needs of a large-scale cluster, you can click Show Advanced Options and select an advanced security group in the **Security Group** field when you create the large-scale cluster.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports disk encryption and the CSI plug-in|ACK allows you to encrypt data disks. You can enable data disk encryption when you create a cluster. This feature can automatically encrypt the data that is transmitted from an ECS instance to a disk and automatically decrypt the data when it is read. This improves data security. Kubernetes V1.14.6 supports the standard CSI drivers. You can select FlexVolume or CSI when you create a cluster.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md), [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md)|

## August 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports Kubernetes V1.14.6|ACK supports Kubernetes V1.14.6. You can select Kubernetes V1.14.6 when you create a cluster. You cannot upgrade an existing cluster to V1.14.6.|All regions|[Kubernetes release notes](https://v1-14.docs.kubernetes.io/docs/setup/release/notes/)|
|Supports more regions for ASK clusters|ASK allows you to create containerized applications without managing or maintaining cluster nodes. You are charged based on the actual ECI resources that are consumed by applications. With ASK clusters, you can focus on the design and development of applications, instead of managing the underlying infrastructure.

|Singapore \(Singapore\)

China \(Hong Kong\)

Australia \(Sydney\)

|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|Releases ASK V2.0 and supports more native Kubernetes features|ASK 2.0 supports multiple namespaces, custom resource definitions \(CRDs\), RBAC, persistent volumes \(PVs\), and persistent volume claims \(PVCs\). ASK 2.0 improves the security and isolation capability of clusters. The average price of ASK clusters is reduced by 46% thanks to lower ECI costs. This includes a 30% reduction in CPUs and a 65% reduction in memory.|All regions|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|Supports creating ACK clusters based on Super Computing Cluster \(SCC\) resources|SCCs are developed based on Elastic Compute Service Bare Metal \(EBM\) instances and adopt the high-speed Remote Direct Memory Access \(RDMA\) technology. SCCs improve network performance. SCCs are used in scenarios such as high-performance computing, artificial intelligence, machine learning, scientific and engineering computing, data analysis, and audio and video processing. You can create SCC-based ACK clusters. This type of cluster combines high-performance infrastructure resources with lightweight and agile containers. SCC-based ACK clusters are applicable to high network throughput and compute-intensive scenarios.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports creating multiple scaling groups and configuring policies to schedule resources across multiple zones|The auto scaling feature is optimized. You can configure multiple scaling groups so that resources of different specifications are displayed when the threshold is reached. This feature meets the requirements of running compute-intensive applications and GPU computing tasks. When you configure auto scaling policies, you can specify different scheduling policies for multiple zones, including priority policies, cost optimization policies, and zone balancing policies. This meets your resource scheduling needs when the cluster is deployed across multiple zones.|All regions|[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|Supports custom cluster domains|ACK allows you to customize a cluster domain by specifying the cluster-domain parameter. The cluster-domain parameter specifies the local domain name that is used to access the service. If you have multiple clusters, customize the local domain names. This helps you manage clusters and services. ACK allows you to customize cluster domains when you create clusters. This simplifies the management process and improves the O&M efficiency.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports Cloud Native App Hub in App Catalog|Cloud Native App Hub provides various open source containerized applications. Container Service App Catalog is integrated with Cloud Native App Hub. To deploy an application to your cluster, use the following steps: Log on to the Container Service console, choose **App Catalog** \> **App Hub**, and then select the application. You do not need to deploy the application by using the CLI tool.|All regions|[View the application catalog](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/App catalog management/View the application catalog.md)|
|Releases a new CCM version|CCM is the core component in a cluster and is responsible for managing various cloud resources, such as SLB instances and VPCs. The following features are provided: -   Supports configuring access control when you create SLB instances. You can specify an IP whitelist for an SLB instance that is created by using ACK. This enhances the security of the cluster.
-   Supports specifying whether to remove the nodes that are set to unschedulable when you run the kubectl cordon or kubectl drain command. Cordoning and draining nodes are important features in cluster maintenance. However, the Kubernetes community does not reach an agreement on whether to remove a node from the backend of an SLB instance when the node is set to unschedulable for maintenance. CCM provides an interface that allows you to specify whether to remove the node from the backend of the SLB instance. This ensures the flexibility of maintenance.
-   Supports mounting pods to the backend of an SLB instance through the Terway ENI. Terway ENI is the latest network plug-in that is provided by ACK. The core feature of Terway ENI is to mount the ENI IP address of a node to a pod. CCM supports mounting pods instead of nodes to the backend of an SLB instance. This prevents traffic forwarding through nodes and improves network performance.
-   Supports setting node weights based on the number of pods on the node for services in Local mode. CCM can adjust the percentage of traffic that is sent to each node based on the number of pods on the node. This helps balance the workloads among nodes. This feature applies to only the services in Local mode.

|All regions|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|

## July 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Releases managed edge clusters for public preview|Managed edge clusters are released for public preview. You can add edge nodes or Edge Node Service \(ENS\) nodes to managed edge clusters. This type of cluster supports edge computing and manages edge nodes and ENS nodes to help you reduce O&M costs. This type of cluster also supports autonomous edges and networks to meet the needs of different edge computing scenarios. You can select this type of cluster on the cluster template page.|China site|-|
|Releases the multi-cluster management feature for public preview|ACK supports multi-cluster management. You can select **Register Kubernetes Cluster** on the cluster template page to add Kubernetes clusters from data centers and Alibaba Cloud to the ACK console. Then, you can deploy applications to these clusters in the console. You can manage hybrid cloud clusters and clusters that are deployed across multiple clouds. After you add user-created clusters from data centers to ACK, you can manage these clusters by using the O&M feature that is provided by ACK.|China site|[Register an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md)|
|Supports more regions for managed ACK clusters|You can create managed ACK clusters on the Alibaba Cloud Japan site. -   Saves resources.

You do not need to create master nodes in a managed ACK cluster. If you use another type of cluster, you must create at least three master nodes to ensure high availability.

-   Simplifies O&M.

ACK manages master nodes.

-   Ensures security.

ACK meets various security needs.


|Japan site|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports adding multiple disks to nodes when you create a cluster|ACK allows you to add multiple disks to nodes when you create a cluster. You do not need to mannually add disks. You can add multiple disks on the cluster creation page. ACK formats and mounts one of the requested disks to the docker directory. You can determine how to handle the other disks.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports selecting an existing security group when you create a cluster|ACK allows you to select an existing security group when you create a cluster. You can click Show Advanced Options to specify an existing security group for the VPC network of your cluster. This allows you to use custom security group rules to improve the security of your cluster.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports cluster deletion protection|ACK supports cluster deletion protection to ensure the security of your cluster. In earlier versions, you are required to enter a Short Message Service \(SMS\) verification code when you delete a cluster. However, you may mistakenly delete the cluster by calling API operations. To ensure the security of clusters, ACK supports cluster deletion protection. You can enable this feature when you create a cluster. If this feature is enabled, you cannot delete the cluster in the console or by calling API operations. To delete the cluster, you must disable this feature. You can enable or disable this feature on the cluster details page.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports authorizing multiple users at the same time|ACK supports authorizing multiple Resource Access Management \(RAM\) users at the same time and managing the permissions on all clusters. You can efficiently authorize RAM users. The authorization procedure of ACK is also optimized.|All regions|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Overview.md)|
|Supports synchronizing time zones among nodes when you create an application|You can select the **Synchronize the Timezone from the Node to the Container** check box when you create an application from an image. This ensures that the pods running the application and the host node have the same time zone.|All regions|[Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md)|
|Supports more regions for Container Registry Enterprise Edition|You can use Container Registry Enterprise Edition in the UK \(London\) region. Container Registry Enterprise Edition supports large-scale image distribution with enhanced security. This service is suitable for enterprise users that require high security and large-scale nodes.|UK \(London\)|-|
|Supports Helm charts for Container Registry Enterprise Edition|Container Registry Enterprise Edition supports Helm charts V2. This helps you manage cloud-native assets. You can enable the charts component on the Overview page of your Enterprise Edition instance. When the component is in the running state, you can start managing Helm chart repositories.|All regions|-|

## June 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Support more regions for managed ACK clusters|You can create managed ACK clusters in the Japan \(Tokyo\) and UK \(London\) regions on Alibaba Cloud.|Japan \(Tokyo\)

UK \(London\)

|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|Enables Terway to share one ENI among multiple pods|Terway of the latest version allows you to assign one ENI to each pod or share one ENI among multiple pods. The default mode is One ENI for Multi-Pod. -   One ENI per Pod: In this mode, the number of pods that can be deployed on a node must match the number of ENIs that can be created on the node. This mode improves network performance.
-   One ENI for Multi-Pod: In this mode, you can deploy multiple pods on a node. The pods share the same ENI.

|All regions|[t64408.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md)|
|Supports Knative|Knative is a Kubernetes-based serverless framework. Knative creates a cloud-native and cross-platform orchestration standard for serverless applications. Knative implements this standard by integrating the creation of containers \(or functions\), workload management \(automatic scaling\), and event models. ACK supports Knative and allows you to install and upgrade the Build, Serving, and Eventing components. Note that you must deploy Istio before you use Knative. ACK also provides an instruction to deploy sample applications, and best practices of tracing, monitoring, and logging applications.|All regions|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Overview.md), [t474495.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Deploy a Hello World application.md)|
|Supports searching for pods by node IP or pod IP|You can search pods by node IP address or pod IP address on the Pods page. In the ACK console, choose **Applications** \> **Pods** and specify a node IP address or pod IP address.|All regions|-|

## May 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions on Alibaba Cloud and Alibaba Finance Cloud for managed ACK clusters|Managed Kubernetes clusters are available in the Australia \(Sydney\) region on Alibaba Cloud and the China East 2 Finance region on Alibaba Finance Cloud. You can create managed Kubernetes clusters in the Australia \(Sydney\) region on Alibaba Cloud and the China East 2 Finance region on Alibaba Finance Cloud.

|Australia \(Sydney\)

China East 2 Finance

|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|Releases genomics computing clusters|ACK supports dedicated genomics computing clusters. This type of cluster uses high performance computing \(HPC\) instances as worker nodes and provides a large-scale workflow engine. Dedicated genomics computing clusters are used to batch process BCL, FASTQ, BAM, SAM, and VCF data. They are also used for data assembly and mutation detection. In the ACK console, choose **Clusters** \> **Clusters**, and click Create Kubernetes Cluster. On the Select Cluster Template page, select **Genomics Computing Cluster** to create a cluster.|All regions|-|
|Releases FPGA clusters to accelerate image and video processing|ACK supports FPGA clusters. This type of cluster uses FPGA F3 instances as worker nodes and is used for H265 video encoding and image conversion from JPEG to HEIF. FPGA-based video encoding reduces the processing time from more than one week to 15 minutes. This reduces the bitrate and saves the bandwidth when you transcode videos of the same quality. In the ACK console, choose **Clusters** \> **Clusters**, and click Create Kubernetes Cluster. On the Select Cluster Template page, select **Dedicated FPGA Cluster** to create a cluster.|All regions|-|
|Releases a new Cloud Controller Manager version|CCM is upgraded to V1.9.3.110-g4938309-aliyun. This version supports more SLB configuration options. The following features are supported: -   Supports setting parameters to determine whether public-facing SLB instances can be created.
-   Supports changing the certificate ID.
-   Supports specifying a VSwitch when you attach an internal SLB instance to a service.
-   Supports configuring SLB instances to redirect traffic from HTTP to HTTPS.

|All regions|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|Upgrades Istio to V1.1.4 and supports Time Series Database \(TSDB\)|Istio V1.1.4 improves self-recovery capabilities and supports automatic recovery of the control plane and automatic upgrade of earlier versions. Istio is integrated with TSDB. TSDB is a database service that supports high-speed read and write, compressed storage, and real-time computing. To address the problems of local storage in Prometheus, TSDB offers remote storage services with high performance and high reliability at low costs.

Compared with other open source remote storage solutions, TSDB is easier to use and only requires you to change Prometheus configurations. The solution supports parallel read and write and is highly compatible with PromQL. TSDB is a distributed storage system with auto scaling capabilities.

|All regions|-|
|Supports synchronizing images across all the regions|Container Registry Enterprise Edition supports synchronizing images across all regions. This solves the issues in the global delivery of applications and helps enterprises improve the iteration efficiency of businesses. Container Registry Enterprise Edition supports large-scale image distribution with enhanced security. It is suitable for enterprises who require high security and large-scale nodes.|All regions|-|
|Supports deploying a cluster across multiple zones and provides five master nodes to ensure high availability|You can select multiple zones when you create a cluster. You can also configure five master nodes for a dedicated cluster. This improves the availability of clusters.|All regions|-|

## April 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports Kubernetes V1.12.6|Kubernetes V1.12.6 is available in all regions. You can upgrade managed or dedicated ACK clusters from Kubernetes V1.11.5 to V1.12.6 in the console.|All regions|-|
|Supports audit logs for managed Kubernetes clusters|You can collect audit logs of managed ACK clusters. Audit logs record operations on the API server and help cluster administrators trace the activities of different users.|All regions|[t21467.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the cluster auditing feature.md)|
|Supports Istio 1.1 to achieve application management in the console|ACK supports Istio V1.1 and allows you to manage Istio applications in the console. You can create and manage Istio applications and services by using a graphical interface. You can create different application versions and implement phased releases with different policies. You can also configure fault injection policies to test the resilience of your services. In the console, choose **Service Mesh** \> **Virtual Services**, and configure the settings.

|All regions|[Traffic management](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Traffic management.md)|
|Supports GPU pods for ASK clusters|You can create applications that run on pods with GPU capabilities in ASK clusters. When you create an application from a template, specify the pod type as GPU in the YAML file.|All regions|[t220322.md\#](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Serverless cluster best practices/Use GPU-based ECIs.md)|
|Supports more regions for Container Registry Enterprise Edition|You can use Container Registry Enterprise Edition in the China \(Beijing\) region.|China \(Beijing\)| |
|Releases FPGA clusters to accelerate image and video processing|ACK supports FPGA clusters. This type of cluster uses FPGA F3 instances as worker nodes and is used for H265 video encoding and image conversion from JPEG to HEIF. FPGA-based video encoding reduces the processing time from more than one week to a short time period. This out-of-the-box service reduces the bitrate and saves the bandwidth when you transcode videos of the same quality. In the ACK console, choose **Clusters** \> **Clusters**, and click Create Kubernetes Cluster. On the Select Cluster Template page, select **Dedicated FPGA Cluster** to create a cluster.|All regions|-|

## March 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions for managed ACK clusters|You can create managed ACK clusters in the China \(Zhangjiakou\), China \(Hohhot\), US \(Silicon Valley\), and Germany \(Frankfurt\) regions.|China \(Zhangjiakou\)

China \(Hohhot\)

Germany \(Frankfurt\)

US \(Silicon Valley\)

|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|Releases Container Registry Enterprise Edition|Container Registry Enterprise Edition was officially released at the Alibaba Cloud Summit on March 21, 2019. This edition offers higher security and supports large-scale image distribution. The public preview of Container Registry Enterprise Edition is available in the China \(Shanghai\) region. To use the service, submit a ticket.|China \(Shanghai\)|[Introduction to Container Registry](https://www.alibabacloud.com/help/zh/doc-detail/60945.html)|
|Container Registry Shared Edition supports more regions on the Alibaba Cloud International site|Container Registry Shared Edition is available in all regions on the Alibaba Cloud International site.|All regions|[Introduction to Container Registry](https://www.alibabacloud.com/help/zh/doc-detail/60945.html)|
|Supports Kubernetes V1.12.6|ACK supports Kubernetes V1.12.6. You can select Kubernetes V1.12.6 when you create a new cluster.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports the Log Service plug-in for managed Kubernetes clusters|Managed ACK clusters support the Log Service plug-in. You can enable Log Service when you create a managed ACK cluster. After the plug-in is installed, you can use Log Service to manage Kubernetes logs.|All regions|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports Windows containers for managed Kubernetes clusters|Managed ACK clusters support Windows containers. You can create managed ACK clusters to deploy Windows containers in the console or by calling API operations. This allows you to deploy traditional Windows applications on cloud-native platforms to achieve agility and elasticity.|All regions|Windows-based clusters are no longer supported.|
|Supports IPVS for ACK clusters|ACK supports the IPVS kube-proxy mode. Compared with the traditional iptables mode, the IPVS mode improves the load balancing performance in large-scale clusters. You can use this mode in all clusters and all regions.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports cluster templates|ACK provides multiple cluster templates in the console. You can select cluster templates based on your business needs. Supported templates include managed ACK clusters, ECS Bare Metal clusters, clusters with GPU capabilities, and Windows clusters. Cluster templates help you create and customize ACK clusters.|All regions|-|
|Supports high performance ECI instance types for ASK clusters|ASK clusters support high performance ECI instance types for genomics computing. The maximum CPU specification is increased from 8 vCPUs to 64 vCPUs. The maximum ECI specification is 64 vCPUs and 256 GiB and the minimum specification is 0.25 vCPU and 0.5 GiB. You can select an instance type based on business requirements to achieve the highest cost efficiency.|All regions|[Limits](https://www.alibabacloud.com/help/zh/doc-detail/89138.html)|

## February 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions for managed ACK clusters|Managed ACK clusters provide the following benefits: -   Saves resources. You do not need to create three master nodes in a managed ACK cluster.
-   Simplifies O&M. ACK manages the master nodes.
-   Ensures security. ACK meets various security needs.

|China \(Shenzhen\)|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports Knative add-ons in App Catalog|Knative is a scale-to-zero and request-driven compute runtime based on Kubernetes and Istio. Knative supports deploying serverless applications and functions.

ACK supports Knative add-ons to help you build the Knative serving environment in your cluster.

|All regions|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Overview.md)|
|Supports cluster check|Cluster check helps you identify the causes of errors in the cluster by performing in-depth checks on cluster resources, components, and configurations.|Mainland China \(Great China\)|[t159904.md\#](/intl.en-US/Best Practices/Operation and maintenance/Use cluster check to troubleshoot cluster issues.md)|

## January 2019

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Releases Windows containers for internal preview|ACK supports Windows containers. This allows Windows applications to run on Kubernetes based on elastic scheduling.

You can add Windows nodes to standard ACK clusters and managed ACK clusters.

Windows containers are released for internal preview. To use Windows containers, submit a ticket.

|All regions|[Create a node pool that runs Windows](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a node pool that runs Windows.md)|
|Releases Container Registry Enterprise Edition for internal preview|Container Registry Enterprise Edition offers container image repositories based on dedicated resources. The edition provides secure enterprise-level image hosting services and large-scale image distribution capabilities. It is suitable for enterprises who require high security and large-scale nodes. Container Registry Enterprise Edition is released for internal preview. To use the service, submit a ticket.

|All regions|-|
|Supports more regions for intelligent cluster O&M|Intelligent O&M provides best practices for cluster management in different scenarios. This helps you identify the causes of errors in the cluster by performing in-depth checks on cluster resources, components, and configurations.|China \(Hangzhou\)|[t159904.md\#](/intl.en-US/Best Practices/Operation and maintenance/Use cluster check to troubleshoot cluster issues.md)|
|Supports Application Real-Time Monitoring Service \(ARMS\)|ACK is integrated with ARMS. After you install the ARMS agent, you can monitor the performance of applications in the cluster. ARMS is a monitoring service for application performance management \(APM\). To monitor a Java application, you only need to attach an ARMS agent to the startup script of the application. No code change is required. ARMS allows you to locate failed API operations or slow calls, reproduce request parameters, detect memory leaks, and discover system bottlenecks. This improves the efficiency of troubleshooting.

|All regions|[Monitor application performance](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/Monitor application performance.md)|
|Releases ECIs in ASK clusters for commercial use|On January 22, 2019, ECI is released for commercial use. Fees are charged when you create pods in ASK clusters. ASK clusters remain free to use.|All regions|[Billing](https://www.alibabacloud.com/help/zh/doc-detail/89142.html)|
|Supports more regions for ASK clusters|You can create ASK clusters in the China \(Beijing\) and China \(Shenzhen\) regions.|China \(Beijing\)

China \(Shenzhen\)

|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|

## December 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions for ACK clusters|You can create ACK clusters in all regions on the China site \(aliyun.com\) and in the UK \(London\) region on the International site \(alibabacloud.com\) site.|UK \(London\)|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports more regions for managed ACK clusters|You can create managed ACK clusters in the China \(Shanghai\), Malaysia \(Kuala Lumpur\), and India \(Mumbai\) regions.|China \(Shanghai\)

Malaysia \(Kuala Lumpur\)

India \(Mumbai\)

|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports removing nodes from a cluster|You can remove nodes from a cluster and decide whether to release the ECS instances on the node.|All regions|[Remove nodes from an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Remove nodes from an ACK cluster.md)|
|Supports deploying DaemonSet applications|You can create DaemonSet applications. DaemonSet is a daemon process that ensures all nodes run one copy of a pod.|All regions|-|
|Supports custom Istio gateways|You can customize Istio ingress and egress gateways by configuring different parameters.|All regions|-|
|Supports Istio CoreDNS|ACK supports using a CoreDNS plug-in to read Istio service entries and associate the IP addresses of the services to their host addresses.|All regions|-|
|Supports adding existing ECS instances to managed ACK clusters|You can select existing ECS instances as nodes when you create a managed ACK cluster.|All regions|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|

## November 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions for managed ACK clusters|You can create managed ACK clusters in the Indonesia \(Jakarta\) region on the Alibaba Cloud International site.|Indonesia \(Jakarta\)|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports Terway|ACK supports the Terway plug-in. Terway enables direct communication between containers through ENIs and offers higher network performance than Flannel.|All regions|[t64408.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Use Terway.md)|
|Supports using thumbnail images to display the performance metrics of worker nodes|ACK uses thumbnail images to display the performance metrics of worker nodes. This allows you to view the node status with ease.|All regions|-|
|Supports adding multiple existing nodes at the same time|You can add multiple existing nodes to a cluster at the same time.|All regions|-|
|Supports rolling renewal of cluster certificates|ACK supports rolling renewal of cluster certificates.|All regions|-|

## October 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions on Alibaba Finance Cloud|You can create ACK clusters in the China South 1 Finance region on Alibaba Finance Cloud.|China South 1 Finance|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports more regions for managed ACK clusters|-|Regions outside mainland China|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Supports management and rollback of deployment versions|ACK provides version management and rollback capabilities to help you manage deployments.|All regions|-|
|Integrates Istio add-ons|ACK is integrated with Istio.|All regions|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Overview.md)|

## September 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports Kubernetes V1.11|-   Provides new features such as CRD upgrade, CoreDNS GA, pod priority settings, and preemptive scheduling.
-   Supports multiple versions such as Kubernetes 1.10 and 1.11.
-   Supports multi-container applications and stateful applications in the console.

|All regions|[t21663.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateful application.md)|
|Supports pulling images from Container Registry private repositories without passwords|ACK allows you to pull images from Container Registry private repositories without passwords.|All regions| |
|Supports auto scaling of nodes|ACK provides the auto scaling component for nodes to automatically scale in and out. General purpose instances, GPU-accelerated instances, and preemptible instances can be automatically added to or removed from an ACK cluster based on your requirements. This feature is applicable to instances that are deployed across multiple zones and diverse instance types. This feature also supports different scaling modes.|All regions|[t220155.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|Supports preemptible instances|-|All regions| |

## August 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Releases managed ACK clusters for public preview|Managed ACK clusters are released for public preview.|All regions|[Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)|
|Releases Istio add-ons|Istio add-ons are released.|All regions|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Overview.md)|

## July 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions for ACK clusters|-|Australia \(Sydney\)|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports canary release and phased release|-|All regions|[Overview](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Overview.md), [Implement a phased release in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Implement a phased release in the ACK console.md)|

## June 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions for ACK clusters|-|Japan \(Tokyo\)

China \(Hohhot\)

|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Enables FPGA and HugePages for ACK V1.10|-|All regions|-|
|Supports application monitoring and alerting|ACK supports application monitoring and alerting.|All regions|[Integration and usage with CloudMonitor]()|
|Supports the subscription billing method|-|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Supports exec and attach commands and ingresses for ASK clusters|-|All regions|[Features](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Features.md)|

## May 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports more regions on Alibaba Finance Cloud|You can create ACK clusters in the China East 2 Finance region on Alibaba Finance Cloud to meet the security and compliance requirements.|China East 2 Finance|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Releases ASK|-|All regions|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|Supports blue-green release, canary release, and A/B testing|-|All regions|[Overview](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Overview.md)|

## April 2018

|Feature|Supported region|Documentation|
|-------|----------------|-------------|
|Supports ACK V1.9 in five regions in Southeast Asia, Middle East, and India|Malaysia \(Kuala Lumpur\)

Indonesia \(Jakarta\)

Singapore \(Singapore\)

India \(Mumbai\)

UAE \(Dubai\)

|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|Updates Service Catalog to support MySQL, RDS, RabbitMQ, and Spark|All regions|[Overview]()|
|Updates App Catalog to support managing applications that are released by using Helm|All regions|[Manage a Helm-based release](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage a Helm-based release.md)|

## March 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports ACK V1.9 and custom ECS instance images|ACK supports native Kubernetes V1.9.3 and releases Workloads API. Custom Resource Definition \(CRD\) is enabled by default and GPU scheduling is supported. You can select custom ECS instance images when you create a cluster. You can also reset images when you add nodes to a cluster.|All regions|-|
|Supports deploying applications by using Helm|ACK supports App Catalog that allows you to deploy applications by using Helm.|All regions|[Manage a Helm-based release](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage a Helm-based release.md)|
|Supports ServiceBroker|ACK provides Service Catalog and supports ServiceBroker.|All regions|[Overview]()|
|Supports monitoring nodes by using Cloud Monitor|ACK allows you to monitor cluster nodes by using Cloud Monitor.|All regions|[Monitor basic resources](/intl.en-US/User Guide for Kubernetes Clusters/Monitoring management/Monitor basic resources.md)|

## January 2018

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Releases ACK and Container Registry on the Alibaba Cloud International site|-|Regions outside mainland China|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|Supports Kubernetes 1.8.4 and provides features such as security enhancement and auto scaling|-|All regions|[t220155.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|Releases FlexVolume to support disks, NAS volumes, and OSS buckets|ACK provides the FlexVolume plug-in to mount cloud disks, NAS volumes, and OSS volumes|All regions|[Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md), [t18764.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/Use NAS volumes.md), and [Use an OSS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Use an OSS volume.md)|
|Supports specifying network policies and bandwidth|ACK allows you to specify network policies and bandwidth.|All regions|[Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md)|
|Supports ECS Bare Metal instances|-|All regions|-|

## October 2017

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports native Kubernetes|ACK supports Kubernetes V1.8.1.|All regions|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|Releases blockchain solutions for public preview|-|All regions|None|

## August 2017

|Feature|Description|Supported region|Documentation|
|-------|-----------|----------------|-------------|
|Supports Kubernetes 1.7.2|-|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|


# Release notes

This topic lists the latest changes to Container Service for Kubernetes \(ACK\).

-   ACK supports Kubernetes 1.18.8, 1.16.9, and 1.14.8. However, to use Kubernetes 1.14.8, you must submit a ticket to apply for this version to be enabled on your account.

-   ACK supports the following operating systems: CentOS 7.7, AliyunLinux 2.1903, and Windows Server 2019.

## December 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports the China \(Guangzhou\) region.|ACK is now available in the China \(Guangzhou\) region.

|China \(Guangzhou\)|[Limits](/intl.en-US/Product Introduction/Limits.md)|
|ACK supports hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters.|You can perform hot migration from existing standard managed Kubernetes clusters to professional managed Kubernetes clusters. Your workloads are not affected during the migration.Professional managed Kubernetes clusters are developed, enhanced, and optimized on top of managed Kubernetes clusters. Professional managed Kubernetes clusters offer higher reliability and security in large-scale production environments for enterprise users. Professional managed Kubernetes clusters are also covered by service-level agreements \(SLAs\) that support compensation.

|All regions

|[Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Hot migration from standard managed Kubernetes clusters to professional managed Kubernetes
         clusters.md)|
|ACK supports multiple specifications of the Server Load Balancer \(SLB\) instances that are used to access a cluster.|You can select a specification for the SLB instance that is used to access the API server when you create an ACK cluster. You can select different SLB specifications based on your business requirements. This allows you to meet different network traffic loads on the API server of the cluster.

|All regions

|[Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md)|
|ACK supports preemptible instances for node pools.|You can use preemptible instances when you set the billing method of a node pool. Preemptible instances are cost-effective. You can bid for unused resources of Alibaba Cloud, obtain the resources, and then run containers until the container resources are reclaimed due to higher bids from other customers. This reduces the costs of elastic container instances in some scenarios.|All regions

|None|
|ACK supports upgrade to Kubernetes 1.18.|You can upgrade the Kubernetes version that is used by ACK from Kubernetes 1.16 to 1.18.|All regions

|[Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Upgrade a cluster.md)|
|ACK supports Cron Horizontal Pod Autoscaler \(CronHPA\) for workloads.|You can enable CronHPA for your workloads in the ACK console. You must install ack-kubernetes-cronhpa-controller in the cluster before you enable CronHPA.|All regions

|[CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md)|
|ACK supports CentOS 7.8 as the node operating system \(OS\).|You can use CentOS 7.8 as the node OS when you create a cluster or a node pool.|All regions

|[Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md)|
|ACK supports reinforcement based on classified protection for the node OS.|ACK supports reinforcement based on classified protection for the cloud native Alibaba Cloud Linux OS in compliance with Multi-Level Protection Scheme \(MLPS\) 2.0 level 3 standards. The following features are provided:-   Identity authentication
-   Access control
-   Security auditing
-   Intrusion prevention
-   Malicious code protection

To enable reinforcement based on classified protection for the node OS when you create a cluster or a node pool, you must select Alibaba Cloud Linux 2.1903 as the node OS and select **Reinforcement based on classified protection**.

|All regions

|None|
|CSI supports volume snapshots created from disks.|The CSI component of ACK supports volume snapshots created from disks. This allows you to back up and restore workload data.|All regions

|[Use volume snapshots created from disks](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use volume snapshots created from disks.md)|
|Supports serverless Kubernetes \(ASK\) cluster upgrades and metrics-server, cronhpa-controller, and alb-ingress-controller.|You can upgrade ASK clusters. You can install and manage metrics-server, cronhpa-controller, and alb-ingress-controller on the Add-ons page in the ACK console.|All regions

|None|

## November 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK releases managed node pools.|Managed node pools are provided by ACK and support auto upgrading and auto repairing. This provides centralized, managed, and operations and maintenance \(O&M\)-free lifecycle management on nodes. You do not need to be concerned about the O&M of nodes, such as component upgrading, OS upgrading, and patching to fix Common Vulnerabilities and Exposures \(CVE\)-identified vulnerabilities. ACK automatically fixes node exceptions for nodes in a managed node pool.

Managed node pools are supported by professional managed Kubernetes clusters.

|All regions

|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Managed node pools/Overview.md)|
|The Helm chart of the Kubernetes dashboard supports Kubernetes 1.18.|The kubernetes-dashboard application provided by App Catalog supports Kubernetes 1.18. This fixes the issue that the pods of Kubernetes 1.18 cannot be accessed by terminals. You can find and install the Helm chart for kubernetes-dashboard in App Catalog.|All regions

|[View the application catalog](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/App catalog management/View the application catalog.md)|
|ACK supports different performance levels for enhanced SSDs when you create a cluster.|You can set the performance level of an enhanced SSD to PL0 or PL1 when you create a cluster. This allows you to customize the performance level of your cluster.

This feature is supported by professional managed Kubernetes clusters, standard managed Kubernetes clusters, dedicated Kubernetes clusters, and managed edge Kubernetes clusters.

|All regions

|[Elastic Block Storage FAQ](/intl.en-US/Block Storage/Elastic Block Storage FAQ.md)|
|ACK upgrades Cloud Controller Manager \(CCM\) to V1.9.3.339-g9830b58-aliyun.|Hash values are supported in the configurations of LoadBalancer type Services. This way, when CCM is restarted, only the backend vServer groups of the related Server Load Balancer \(SLB\) instances are synchronized if the Service configuration is not changed. The configurations of the related SLB instances and listeners are not synchronized.|All regions

|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|CSI supports the monitoring of cloud disks.|The latest version of the CSI component supports cloud disk monitoring. This feature allows you to monitor the states of persistent volume claims \(PVCs\) through Application Real-Time Monitoring Service \(ARMS\) Prometheus when you use the cloud disks that are mounted through the PVCs. You can also configure alerts by setting thresholds for the storage space and input/output operations per second \(IOPS\) of the cloud disks.|All regions

|None|
|ASK supports component management, Ingress controllers, and CoreDNS.|You can install and enable Ingress controllers and CoreDNS when you create an ASK cluster or on the Add-ons page in the ACK console after the cluster is created.|All regions

|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|The hybrid cloud solution supports node pools of ACK in registered external Kubernetes clusters|You can create and use node pools in registered external Kubernetes clusters in the ACK console. You can use node pools to manage a set of Elastic Compute Service \(ECS\) instances for a registered external Kubernetes cluster. You can add ECS instance-based nodes from a node pool to a self-managed Kubernetes cluster or a Kubernetes cluster that is provided by other cloud service providers. You can also use node pools to manage the labels and taints of nodes in node pools.|All regions

|[Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md)|

## October 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports time zone selection when you create a cluster.|You can select the time zone when you create a cluster. By default, the time zone configured for your browser is selected.

The feature is supported by professional managed Kubernetes clusters, standard managed Kubernetes clusters, dedicated Kubernetes clusters, and ASK clusters.

|All regions

|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK allows you to add tags on cloud disks, Network Attached Storage \(NAS\) file systems, and Log Service projects.|CSI and Logtail supports cloud disks, NAS file systems, and Log Service projects with tags. Cloud disks, NAS file systems, and Log Service projects that are created by ACK for a cluster are added with the tag of the cluster ID. This makes the allocation of resource fees easier.|All regions

|None|

## September 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports the China \(Ulanqab\) region.|ACK is now available in the China \(Ulanqab\) region.|All regions

|[ACK Pro overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/ACK Pro overview.md)|
|ACK supports Server Message Block \(SMB\) file systems for containers that run Windows.|ACK allows you to mount an SMB file system to a container that runs Windows. In the NAS console, you can create an SMB file system in the virtual private cloud \(VPC\) where the cluster is deployed. You can also create a mount target for the file system. You must use the FlexVolume plug-in to mount an SMB file system.|All regions

|[Mount SMB file systems to Windows containers](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Mount SMB file systems to Windows containers.md)|
|ACK supports time zone selection when you create a cluster.|You can specify the time zone for master nodes and worker nodes when you create a dedicated or managed Kubernetes cluster.|All regions

|None|
|ACK supports Kubernetes 1.18.|ACK supports Kubernetes 1.18.8. You can select this version when you create a cluster.|All regions

|[Release notes for Kubernetes 1.18](/intl.en-US/Release notes/Kubernetes release notes/Release notes for Kubernetes 1.18.md)|
|ACK allows you to enable or disable the NetworkPolicy feature for Terway when you create a cluster.|You can specify whether to enable the NetworkPolicy feature for Terway when you create a cluster.|All regions

|-   [Use network policies for access control](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Use network policies for access control.md)
-   [Improve the performance of the NetworkPolicy feature for a large ACK cluster in Terway mode](/intl.en-US/User Guide for Kubernetes Clusters/Network/Best practices/Improve the performance of the NetworkPolicy feature for a large ACK cluster in Terway
         mode.md) |
|ACK supports periodic inspections for the security management of a cluster.|You can configure periodic inspection policies for a cluster on the Inspections page in the ACK console.|All regions

|[Use the inspection component to check for security risks in workloads of an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the inspection component to check for workload security risks in an ACK cluster.md)|
|ACK allows you to enable or disable the cluster auditing feature for the security management of a cluster.|You can enable or disable the cluster auditing feature on the Cluster Auditing page in the ACK console.|All regions

|[Use the cluster auditing feature](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the cluster auditing feature.md)|
|ACK supports component management, logtail-ds, migration-controller, and ack-virtual-node for registered external Kubernetes clusters.|The logtail-ds component collects container logs from registered external Kubernetes clusters, including stdout files and log files of containers.

The migrate-controller component is developed based on the open source Velero project. You can use migrate-controller to migrate applications across Kubernetes clusters.

The ack-virtual-node component is used to enable auto scaling for registered external Kubernetes clusters.

|All regions

|-   [Install and use logtail-ds](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install and use logtail-ds.md)
-   [Install migrate-controller](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Manage components/Install migrate-controller.md) |
|ACK upgrades Sandboxed-Container to V2.0.|Sandboxed-Container is upgraded to V2.0. Sandboxed-Container V2.0 has the following benefits:-   Sandboxed-Container is a container runtime that is developed by Alibaba Cloud on top of lightweight virtual machines. Compared with Sandboxed-Container 1.0, Sandboxed-Container 2.0 supports more lightweight and efficient deployment and simplifies the architecture and maintenance of ACK clusters.
-   Sandboxed-Container 2.0 reduces the resource overheads by 90% and accelerates the startup of sandboxed containers by three times.
-   Sandboxed-Container 2.0 increases the deployment density of standalone sandboxed containers by 10 times.
-   Sandboxed-Container 2.0 supports the virtio-fs file system, which provides higher performance than the 9pfs file system.

|All regions

|[Sandboxed-Container overview](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Sandboxed-Container overview.md)|
|ASK supports Knative components.|Knative is a cloud native and cross-platform orchestration engine for serverless applications. You can deploy Knative in ASK clusters. You can use cloud resources by calling the Knative API without the need to pay for the Knative controller.|All regions

|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Overview.md)|

## August 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports the gatekeeper component for OPA.|You can install the gatekeeper component on the Add-ons page in the ACK console. This facilitates the management and enforcement of policies that are executed by Open Policy Agent \(OPA\) in ACK clusters.|All regions

|[Introduction](/intl.en-US/Release notes/System Component change Records/Release notes for gatekeeper/Introduction.md)|
|ACK supports runtime inspections for the security management of a cluster.|You can perform runtime inspections on the Runtime Security page in the ACK console. This feature monitors the container runtime and triggers alerts upon the following types of security events: malicious image startups, attacks by viruses or malicious programs, intrusions into containers, container escapes, and high-risk operations on containers. To use this feature, you must first activate Security Center. If you use a Resource Access Management \(RAM\) user, make sure that the RAM user has the permissions to access Security Center.|All regions

|[Use Runtime Security to monitor ACK clusters and configure alerts](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use Runtime Security to monitor ACK clusters and configure alerts.md)|
|ACK supports scheduled backups for Elastic Block Storage \(EBS\) devices.|You can create scheduled snapshots for cloud disks. To use this feature, you must first install the Storage Operator component.|All regions

|None|
|Terway supports IPVLAN and extended Berkeley Packet Filter \(eBPF\) for lossless network.|If an elastic network interface \(ENI\) is shared among pods, Terway allows you to use IPVLAN and eBPF for network virtualization.Terway enables pod network virtualization powered by the lightweight IPVLAN technology. This allows pod traffic to bypass the network stack of the host and reduces the network performance overheads. Terway uses Cilium as the BPF agent on nodes to configure BPF rules for pod ENIs. This enables Services and network policies to be configured on ENIs, and the requests within pod networks are forwarded to ENIs through IPVLAN. This reduces network complexity.

**Note:** This feature is based on the Alibaba Cloud Linux 2 operating system. To use this feature, you must [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply for this feature to be enabled for your account.

|All regions

|[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)|
|ACK supports professional managed Kubernetes clusters in the China \(Beijing\), China \(Shenzhen\), Germany \(Frankfurt\), Indonesia \(Jakarta\), and China East 2 Finance regions \(on Alibaba Finance Cloud\).|Professional managed clusters are now available in the China \(Beijing\), China \(Shenzhen\), Germany \(Frankfurt\), Indonesia \(Jakarta\), and China East 2 Finance regions \(on Alibaba Finance Cloud\).|China \(Beijing\), China \(Shenzhen\), Germany \(Frankfurt\), Indonesia \(Jakarta\), and China East 2 Finance \(on Alibaba Finance Cloud\)|[ACK Pro overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/ACK Pro overview.md)|
|ACK@Edge is released for commercial use.|ACK@Edge is a cloud-managed solution that is provided by ACK to coordinate cloud and edge computing.|All regions

|[ACK@Edge overview](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md)|

## July 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK releases the public preview of professional managed Kubernetes clusters.|Professional managed Kubernetes clusters are developed on top of managed Kubernetes clusters. Professional managed Kubernetes clusters provide higher reliability and security in large-scale production environments for enterprise users. Professional managed Kubernetes clusters are also covered by service-level agreements \(SLAs\) that support compensation. This type of cluster is suitable for the following users:-   Internet enterprises. These enterprises deploy their business in large-scale production environments and require business management with high stability, security, and observability.
-   Big data computing enterprises. These enterprises deploy large-scale data computing services, high-performance data processing services, and other services with high elasticity. These services require clusters with high stability, high performance, and efficient computing capabilities.
-   Foreign enterprises who run their business in China. These enterprises hold the standard compensation SLA and security in high regard.
-   Financial enterprises. These enterprises require SLAs that support compensation.

|All regions

|[ACK Pro overview](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/ACK Pro overview.md)|
|ASKupports the Japan \(Tokyo\) and Indonesia \(Jakarta\) regions.|ASK clusters are now available in the Japan \(Tokyo\) and Indonesia \(Jakarta\) regions.|Japan \(Tokyo\) and Indonesia \(Jakarta\)|[Introduction](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Introduction.md)|
|ACK upgrades CCM to V1.9.3.313-g748f81e-aliyun.|CCM provides the following new features:-   Supports deletion protection for SLB instances. By default, deletion protection is enabled for newly created SLB instances.
-   Supports modification protection for the configurations of SLB instances. By default, modification protection is enabled for the configurations of newly created SLB instances.
-   Allows you to specify the resource group for an SLB instance when you create a Service.
-   Allows you to specify the name of an SLB instance when you create a Service.
-   Allows you to mount pods in Terway mode to the backend of an SLB instance.

|All regions

|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|ACK supports security management for clusters, including security policies and cluster inspections.|ACK provides security management for your clusters. You can configure pod security policies and cluster inspections.Pod security policy is a significant method to verify the security of pod configurations before pods are deployed. This ensures that applications are running in secure pods. Cluster inspection detects the security risks of workloads in an ACK cluster and generates inspection reports for your reference. This way, you can check whether the workloads in your ACK cluster are running in a secure environment.

|All regions

|[Use a PSP](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use a PSP.md)|
|ACK supports VPC sharing.|VPC sharing allows multiple accounts to create cloud resources in a VPC. The cloud resources include ECS instances, SLB instances, and ApsaraDB for RDS instances. This provides a unified approach for you to manage cloud resources in a shared VPC. This feature is powered by the resource sharing mechanism. The Alibaba Cloud account that owns a VPC can share all vSwitches in the VPC with other accounts under the same organization. You can select a shared VPC when you create an ACK cluster. If you select a shared VPC for an ACK cluster, you can use only Terway as the network plug-in.|All regions

|None|
|ACK supports cluster registration.|During daily O&M, you may need to deploy multiple clusters in the cloud and data centers. In some scenarios, you may even deploy clusters in different clouds that are provided by different cloud service providers. In these cases, you can register external Kubernetes clusters in the ACK console. This allows you to manage external Kubernetes clusters in the console and reduce O&M costs.|All regions

|[Introduction to registering external clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Introduction to registering external clusters.md)|
|ACK supports redeployment and rollback of workloads.|ACK provides new features on the workload management page in the ACK console, such as application redeployment and rollback. This allows you to perform more operations on your workloads.|All regions

|[Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md)|

## June 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports taint management for node pools.|You can configure taints when you create or edit a node pool. This allows you to add taints to all nodes in the node pool. You can select **Synchronize Node Labels and Taints** to update taints for existing nodes in a node pool.|All regions

|[Manage taints](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Manage taints.md)|
|ACK supports application migration from virtual machines to ACK clusters by using Server Migration Center \(SMC\).|SMC allows you to migrate servers to Container Registry. You can use SMC to migrate containerized applications to Container Registry at low costs.|All regions

|[Migrate source servers to Container Registry](/intl.en-US/Best Practices/Migrate source servers to Container Registry.md)|

## May 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports advanced security groups during cluster creation.|ACK allows you to select different types of security groups when you create a cluster. You can select a basic security group, an advanced security group, or an existing security group. Compared with a basic security group, an advanced security group can contain up to 65,536 private IP addresses. Advanced security groups are used for clusters where a large number of containers or instances are deployed.|All regions|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK supports the Prometheus component and the Kubernetes event center.|ACK is integrated with the most commonly used Prometheus component in the container monitoring field, and the most commonly used node-problem-detector \(NPD\) component in the O&M field. You can select these components when you create a cluster. You can also upgrade and maintain the components on the Add-ons page. Prometheus is provided by Application Real-Time Monitoring Service \(ARMS\). NPD is a tool used for node problem detection. NPD can export events that record node exceptions, such as Docker Engine hangs, Linux kernel hangs, network access issues, and file descriptor issues. You can click the **Event Center** tab on the **Events** page to view event details.|All regions|[Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md)|
|ACK supports Kubernetes 1.16.9.|ACK supports Kubernetes 1.16.9. You can create a cluster of Kubernetes 1.16.9. If the Kubernetes version of your cluster is earlier than V1.16.9, go to the Clusters page and choose **More** \> **Upgrade Cluster** in the Actions column to upgrade to Kubernetes 1.16.9. Compared with the previous Kubernetes 1.16.6, Kubernetes 1.16.9 fixes the CVE-2020-8555 SSRF vulnerability for the kube-controller-manager component.|All regions|[Vulnerability fixed: CVE-2020-8555 in kube-controller-manager](/intl.en-US/Bulletin/Security bulletins/Vulnerability fixed: CVE-2020-8555 in kube-controller-manager.md)|
|ACK supports elastic workloads.|ACK supports elastic workloads. On the **App Catalog** page, select **ack-kubernetes-elastic-workload** to install the component. You can use ACK and Virtual Kubelet in combination to proportionally schedule pay-as-you-go and preemptible instances. This allows you to schedule your workloads with elasticity.|All regions|[View the application catalog](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/App catalog management/View the application catalog.md)|
|ACK supports Application Center.|In earlier versions, after applications are deployed, the topology of the applications is not displayed in a unified view. Therefore, version management and rollback cannot be unified for continuous deployments. Application Center provides a unified portal for your applications. This allows you to view the deployment of applications in a global manner. You can also view the deployment status and changes of all ACK sub-resources that are allocated to each application. In addition, Gits and Helm charts are used to deploy applications in ACK clusters based on versions. This allows you to publish or roll back applications deployed in ACK clusters of different versions.|All regions|[Overview]()|

## April 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|Alibaba Cloud Genomics Service \(AGS\) is released for commercial use.|AGS is an ACK-based big data compute service provided by Alibaba Cloud for users in the biological industry. AGS provides efficient, elastic, and reliable services. AGS is faster in computing and more cost-efficient than traditional methods. AGS adopts the pay-as-you-go billing method based on the number of successful API calls in the backend. To submit a computing task, you only need to run a command to call the AGS API on the client. This process is one API call.|All regions|[Overview](/intl.en-US/User Guide for Genomics Service/Overview.md)|
|ACK supports online scale-out for dynamic volumes.|For Kubernetes 1.16 and later, ACK allows you to scale out volumes without restarting pods.|All regions|[Use CSI to dynamically expand a disk PV in ACK](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use CSI to dynamically expand a disk PV in ACK.md)|
|ACK supports deployment of multiple Ingress controllers.|An Ingress is an important entry for Layer 7 services. If you create only one Ingress for a cluster, the routing performance may encounter a bottleneck. If an Ingress allows inbound access through a public network and private network at the same time, security risks exist. To solve these issues, ACK provides a Helm chart for the Ingress controller when only one Ingress is used. The name of the Helm chart is ack-ingress-nginx. You can deploy multiple Ingress controllers from App Catalog. You can use YAML files to configure access to Internet-facing and internal-facing SLB instances iseparately.|All regions|[Deploy Ingresses in a high-reliability architecture](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Deploy a highly reliable Ingress controller.md)|
|ASK supports the India \(Mumbai\) region.|ASK is now available in the India \(Mumbai\) region.|India \(Mumbai\)|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|

## March 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK adds features to component management.|The following features are added to component management: -   Allows you view the YAML files of components.
-   Allows you to perform health checks for nodes before component upgrades. This prevents component upgrade failures that are caused by node drains or exceptions.
-   Allows you to manually refresh the Add-ons page.

|All regions|[Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md)|
|CCM allows you to add custom ECS instances to the backend of SLB instances.|CCM allows you to add self-managed ECS instance-based nodes to the backend of SLB instances. Therefore, the existing applications and containerized applications share the same SLB instances and inbound traffic. This is suitable for scenarios where existing applications are gradually replaced by containerized applications.|All regions|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|Terway allows you to expand clusters and change node specifications.|When you manually expand a cluster, you may need to create nodes in new zones. In earlier versions, to create pods in a new zone, you must first add new pod vSwitches in the zone. You can now add pod vSwitches in Terway ConfigMaps. When you change the specifications of a node, the maximum number of pods that are supported by Terway on the node also changes. After this release, the K8s max-pod parameter is automatically adjusted to fit the new node specifications.|All regions|[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)|
|ACK supports node pool management.|A node pool is a group of nodes with the same configurations. For example, nodes in a node pool are configured with the same container runtime, OS, and security group. You can create multiple node pools for a cluster. This allows you to deploy a variety of services to different node pools in a cluster. Node pools also support auto scaling. Nodes can be automatically added when a node pool is running with insufficient resources.|All regions|[Create a node pool](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Create a node pool.md)|
|ACK optimizes cluster checks.|Cluster check is the core feature provided by ACK for cluster O&M. Cluster check dynamically scans clusters to identify potential risks. The optimization provides the following services: -   Displays information about unknown hosts.
-   Checks the availability of Yellow dogUpdater, Modified \(YUM\).
-   Checks the availability of systemd.

|All regions|[Use cluster check to troubleshoot cluster issues](/intl.en-US/Best Practices/Operation and maintenance/Use cluster check to troubleshoot cluster issues.md)|
|ACK supports upgrades to Kubernetes 1.16.|ACK allows you to create clusters of Kubernetes 1.16.6. You can also upgrade your clusters from Kubernetes 1.14.8 to 1.16.6. We recommend that you refer to the upgrade notes before you upgrade your clusters.|All regions|[Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Upgrade a cluster.md)|
|ACK supports managed Kubernetes clusters in the China South 1 Finance region on Alibaba Finance Cloud.|Managed ACK clusters are now available in the China South 1 Finance region on Alibaba Finance Cloud.|China South 1 Finance|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK allows you to configure ephemeral-storage for containers when you create an application.|ephemeral-storage is a new resource mode similar to CPU and memory. Kubernetes uses this parameter to manage and schedule the transient storage of applications that run in Kubernetes clusters. The root directory and log directories \(/var/log\) of kubelet are stored on the primary partition of a node. In addition, emptyDir volumes, container logs, image layers, and the writable layers of containers are also stored on the primary partition. Therefore, ephemeral-storage is used to manage the primary partition of a node. You can set requests and limits when you create an application. This allows you to schedule and manage the storage resources that are allocated from the primary partition to the application.|All regions|[Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md)|

## February 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports Kubernetes 1.16 and Docker 19.03.5.|ACK supports Kubernetes 1.16 and provides enhanced cloud native capabilities. Compared with the earlier version, Kubernetes 1.16 accelerates pod creation and improves affinity, stability, and observability. You can select Docker 19.03.5 when you create a cluster. ACK accelerates container startups and the building of images that are based on Docker 19.03.5.|All regions|[Kubernetes 1.16 release notes](/intl.en-US/Release notes/Kubernetes release notes/Kubernetes 1.16 release notes.md)|
|ACK supports AliyunLinux 2, custom security groups, and GPU-accelerated preemptible instances for auto scaling.|The auto scaling feature is improved to add the following options: the AliyunLinux2 operating system, custom security groups, and GPU-accelerated preemptible instances. To use the first two features, you must first submit a ticket to apply for the features to be enabled for your account.|All regions|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|ACK supports worker nodes that run CentOS 7.7.|ACK supports CentOS 7.7. You can specify the CentOS 7.7 operating system when you create worker nodes. CentOS 7.7 is automatically used when you expand clusters or enable auto scaling for clusters.|All regions|[Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm). |
|ACK upgrades Helm to V3 in App Catalog.|ACK supports Helm 3. You can install Helm 3 in App Catalog. Compared with Helm 2, Helm 3 improves the security of role assignment, provides full compatibility with Kubernetes role-based access control \(RBAC\) in multi-tenant scenarios, and supports more useful hooks.|All regions|For information about how to upgrade from Helm 2, see [Helm v2 upgrade notice](/intl.en-US/Bulletin/Helm v2 upgrade notice.md).|
|ASK supports the Indonesia \(Jakarta\) and UK \(London\) regions.|ASK is now available in the Indonesia \(Jakarta\) and UK \(London\) regions. You can create ASK clusters in these regions in the ACK console.|Indonesia \(Jakarta\) and UK \(London\)|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|ASK allows you to create ClusterIP type Services.|ASK now supports more options to deploy containerized applications. You can create ClusterIP type Services in ASK clusters. This enables access to your workloads from within an ASK cluster.|All regions|[Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md)|
|CCM allows you to attach both ECS instances and elastic container instances to the backend of SLB instances.|CCM allows you to attach ECS instances and elastic container instances to the backend of SLB instances that are associated with Services. This enables unified scheduling for application pods across worker nodes and virtual nodes. This also improves application resilience.|All regions|[Release notes for CCM](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|Edge Kubernetes clusters support 32-bit and 64-bit ARM nodes.|Edge Kubernetes clusters support more heterogeneous infrastructures. You can add 32-bit and 64-bit ARM nodes to edge Kubernetes clusters. You can add Edge Node Service \(ENS\) nodes or nodes of data centers to edge Kubernetes clusters.|All regions|[Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md)|

## January 2020

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK allows pods that are deployed on virtual nodes to access ClusterIP type Services.|ACK allows pods that are deployed on virtual nodes to access ClusterIP type Services. This enables Kubernetes to centrally manage virtual nodes and elastic container instances. You can deploy applications on virtual nodes without the inconvenience of resource capacity planning. This meets the requirements of scenarios such as online workload scaling, offline computing, and CI/CD, and reduces the overall computing costs. To enable this feature, log on to the console, choose App Catalog, and then find and install ack-virtual-node.|All regions|[Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md)|
|ACK allows you to enable service account token volume projection for the Kubernetes API server.|ACK allows you to enable service account token volume projection for the API server when you create a cluster. This enables service account authentication on pods. This feature is also required if mutual Transport Layer Security \(TLS\) authentication is enabled on Istio through Secret Discovery Service \(SDS\).|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|CSI supports more features.|You can select CSI as the volume plug-in when you create an ACK cluster. The optimized CSI plug-in provides the following features: -   Allows you to mount Object Storage Service \(OSS\) subdirectories to containers.
-   Supports the Memory type emptyDir volumes. The Memory type volume is a RAM-based temporary file system, whose storage space is limited by memory. This type of file system provides good performance and is typically used to provide cache space in containers.
-   Supports accelerated OSSFS transmission. OSSFS allows you to share data by mounting OSS buckets to local file systems in Linux. To meet the requirements in big data and AI scenarios, ACK improves read speed by adjusting concurrency, block size, and libfuse configurations. For more information, see [alibaba-cloud-csi-driver](https://github.com/kubernetes-sigs/alibaba-cloud-csi-driver).

|All regions|[Install CSI](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Install the CSI plug-in.md)|
|ACK improves the storage capabilities of sandboxed containers.|Sandboxed containers enhance cloud native capabilities and support cloud disks and NAS file systems. This allows ACK to provide the same storage performance as when these storage services are used on virtual machines. ACK also supports RootFS BLKIO Limit and disk I/O throttling on pods, and optimizes its support for multi-tenancy.|All regions|[Create an ACK cluster that supports sandboxed containers](/intl.en-US/API Reference/Clusters/Create a cluster/Create an ACK cluster that supports sandboxed containers.md)|
|ACK releases the public preview of confidential computing clusters.|Based on Intel Software Guard Extensions \(SGX\), confidential computing clusters are particularly suitable for sensitive data protection and scenarios such as smart contracts in blockchains, user secrets processing, intellectual property protection, genomics computing in bioinformatics, and edge computing. Currently, you can create confidential computing clusters, manually expand clusters, enable auto scaling, and add different types of nodes to the clusters. For more information, see [Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md) and [SGX application development guide](https://developer.aliyun.com/article/740793). ACK also provides open source sgx-device-plugin to help you deploy SGX applications on Kubernetes clusters. For more information, see [Kubernetes device plugin for Intel SGX](https://github.com/AliyunContainerService/sgx-device-plugin). **Note:** Intel \(R\) Software Guard Extensions \(Intel\(R\) SGX\) are a set of CPU instruction codes that are developed by Intel. Intel\(R\) SGX allows developers to run application code and data in a special runtime environment called enclave, which is built based on hardware silos and memory encryption technology. Enclaves refer to Trusted Execution Environment \(TEE\). No application, OS Kernel, BIOS, or hardware other than the CPU can access an enclave without verification. All data in enclave memory is encrypted. Users encrypt the code and data in an enclave with their private keys obtained from Intel. An enclave can only be started after the signature is verified through remote certification service Intel IAS.

|All regions|[Create a managed Kubernetes cluster for confidential computing](/intl.en-US/User Guide for Kubernetes Clusters/TEE-based confidential computing/Create a managed Kubernetes cluster for confidential computing.md)|
|AGS supports gene sequencing through API operations.|ACK has released a set of API operations for genomics computing. You can call these API operations to submit gene sequencing tasks. Results are automatically uploaded to your OSS buckets. This saves you the inconvenience of cluster creation and task deployments. These APIs support different SLA levels and provide computing resources based on different requirements. This allows you to reduce costs and improve efficiency. Currently, this feature is in public preview. To use the feature, submit a ticket.|All regions|[Use AGS to perform WGS tasks](/intl.en-US/User Guide for Genomics Service/AGS acceleration API/Use AGS to perform WGS tasks.md)|

## December 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports component management for ACK clusters.|ACK allows you to manage cluster components. You can log on to the ACK console. On the **Clusters** page, find the cluster that you want to manage and choose **More** \> **Manage System Components** in the Actions column to manage cluster components. You can manage all system components and optional components with operations such as upgrade, uninstall, and reinstall. More custom configurations will be available soon.|All regions|[Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md)|
|App Catalog supports node local DNS to speed up DNS queries.|App Catalog supports node local DNS, which sends internal DNS queries to CoreDNS and directly forwards external DNS queries to external DNS resolvers. Node local DNS caches all queries and provides DNS caching on each node. This significantly improves the overall DNS QPS of the cluster.|All regions|[View the application catalog](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/App catalog management/View the application catalog.md)|
|ACK supports managed Kubernetes clusters in the China East 1 Finance region on Alibaba Finance Cloud.|Managed Kubernetes clusters are now available in the China East 1 Finance region on Alibaba Finance Cloud. You only need to create worker nodes in a managed Kubernetes cluster. ACK creates and manages master nodes. This type of cluster is easy to use and provides high availability at low costs. You can focus on business development without the trouble of master node O&M.|China East 1 Finance|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK supports neural processing unit \(NPU\)-accelerated ECS instances.|You can select NPU-accelerated ECS instances when you create managed or dedicated Kubernetes clusters. The instance type is ecs.ebman1.26xlarge, which is suitable for big data analytics and AI scenarios in video and graphics industries.|All regions|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK improves the user experience of Terway.|After you select the Terway plug-in, you may want to know the number of pods that are supported by different ECS instance types. The user interface provides information about the number of pods that are supported by each ECS instance type when you create a cluster. When you expand a cluster, the user interface also provides multiple options. This allows you to select vSwitches for nodes and pods. The user interface is optimized to clearly and accurately provide information.|All regions|[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)|

## November 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK allows you to select multiple zones and mount multiple data disks when you expand an ACK cluster.|The user interface for expanding an ACK cluster is updated to provide the same configuration options as those for creating an ACK cluster. You can select multiple zones when you expand an ACK cluster. You can also mount multiple data disks to a node and specify whether to encrypt these disks.|All regions|[Expand an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Expand an ACK cluster.md)|
|ACK supports custom scripts, tags, and Operation Orchestration Service \(OOS\).|You can write custom scripts to configure nodes when you create or expand an ACK cluster. To use this feature, submit a ticket to apply for this feature to be enabled on your account. You can use this feature to specify the node OS. Instead of building custom images, you can directly inject scripts into standard images. Auto scaling allows you to add tags to cluster nodes. This makes it easier for you to identify cluster nodes and allocate the cost of nodes. ACK integrates OOS into the node O&M. You can go to the OOS page from the ACK console and run OOS scripts to maintain nodes on the OOS page.|All regions|[Expand an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Expand an ACK cluster.md)|
|ASK supports multiple zones and log auditing.|After ASK is upgraded to V2.0, ASK clusters provide more cloud native features. ASK now supports multiple zones and log auditing. You can deploy pods across zones to improve the availability of your business. You can also use log auditing to improve the security of ASK clusters. ASK clusters will be improved to provide the same features as dedicated and managed Kubernetes clusters.|All regions|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|ACK supports vGPU resources.|To meet the requirements from AI and big data industries, ACK supports vGPU resources based on the vgn5i instance family. You can select instance types of the vgn5i instance family when you create an ACK cluster.|All regions|[Use GPU-based ECIs](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Serverless cluster best practices/Use GPU-based ECIs.md)|
|ACK enables Terway to support ENI buffer pools.|Terway is a container network plug-in that is developed based on Alibaba Cloud ENI. The update enables Terway to create a buffer pool of ENI IP addresses during node initialization. This accelerates pod creation and improves user experience.|All regions|[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)|
|ACK enables CCM to add external ECS instances to the backend of SLB instances.|CCM is a system component that associates Services with SLB instances. By default, cluster nodes that host Services are mounted to the backend of the related SLB instances. The update allows you to add ECS instances outside an ACK cluster as the backend servers to the related SLB instances. This makes it easier to perform application migration and canary releases.|All regions|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|

## October 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports AliyunLinux2.|AliyunLinux2 is the latest OS version that is developed by Alibaba Cloud based on an advanced CentOS kernel version. AliyunLinux2.1903 is fully adapted to ACK. This OS version supports faster startups and optimized performance, and improves the efficiency and reliability of ACK clusters.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK provides the Ingress dashboard feature.|In earlier versions, you must manually configure the Ingress dashboard, which is a time-consuming and error-prone task. A check box is now added to the configuration page of the Ingress component. You only need to select the check box to enable the Ingress dashboard feature. This way, the Ingress dashboard is automatically installed after the cluster is created.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK allows you to select SLB instance specifications.|In earlier versions, when you create a LoadBalancer type Service, ACK automatically creates shared-performance SLB instances. To meet your requirements in various scenarios, ACK now allows you to select SLB instance specifications when you create a LoadBalancer type Service. The SLB instances adopt the pay-as-you-go billing method.|All regions|[Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Manage Services.md)|
|ACK allows you to bind an EIP to or unbind the EIP from the API server.|SLB instances provide access to the API server of an ACK cluster. When you create an ACK cluster, ACK allows you to specify an Internet-facing or internal-facing SLB instance to handle traffic to the cluster. However, you may want to change the network type of the SLB instance after the cluster is created. ACK allows you to bind an EIP to or unbind the EIP from the SLB instance after the cluster is created. This allows you to change the access mode to the API server between Internet access and internal access.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK@EDGE supports auto scaling of ENS nodes in edge Kubernetes clusters.|To support edge computing scenarios, ACK allows you to configure auto scaling of Edge Node Service \(ENS\) nodes in edge Kubernetes clusters. This feature can be implemented by calling the API.|All regions|[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|ASK supports more regions.|ASK clusters are now available in the China \(Zhangjiakou\) region.|China \(Zhangjiakou\)|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|

## September 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions.|ACK is now available in the China \(Chengdu\) region. You can create dedicated Kubernetes clusters. To create managed Kubernetes clusters, submit a ticket.

|China \(Chengdu\)|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports upgrades from Kubernetes 1.14.6 and improves the upgrade feature.|The canary release of upgrades from Kubernetes 1.14.6 has been implemented in the following regions: China \(Shanghai\), China \(Zhangjiakou\), Singapore \(Singapore\), and Germany \(Frankfurt\). Upgrades FROM Kubernetes 1.14.6 will soon be available in all regions. ACK also provides new features to simplify the upgrade process. In the ACK console, you can click **Upgrade Cluster** on the Clusters page to upgrade your cluster. The new upgrade feature adds the following improvements to secure upgrades:

-   A comprehensive cluster check is performed before an upgrade.
-   You can manually pause or resume an upgrade.
-   Detailed logs of upgrades are retained.

|-   China \(Shanghai\)
-   China \(Zhangjiakou\)
-   Singapore \(Singapore\)
-   Germany \(Frankfurt\)

|[Upgrade a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Upgrade a cluster.md)|
|ACK supports node maintenance.|To maintain nodes in a cluster, you must make sure that workloads are not deployed on the nodes that you want to maintain. ACK supports node maintenance. You can select one or more nodes that you want to maintain and set them to unschedulable on the Nodes page. You can also drain these nodes.

-   After you set a node to unschedulable, pods cannot be scheduled to the node.
-   If you drain a node, no new pods are scheduled to the nodes and existing pods on the node are migrated to other nodes. However, pods that are managed by DaemonSets are not migrated from the node.

If you have a LoadBalancer type Service, you can set the node that runs the pods that are associated with the Service to unschedulable to remove the node from the backend of the related SLB instance. This allows you to flexibly manage your workloads during node maintenance.

|All regions|[Mark a node as unschedulable](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Mark a node as unschedulable.md)|
|ACK supports custom node names.|To manage a cluster that includes a large number of nodes, you must identify nodes by name. The default node names provided by ACK are not easy to identify. ACK now allows you to customize node names when you create a cluster. When you create a cluster in the ACK console, you can select **Custom Node Name** in the advanced settings of the cluster. You can define a prefix, an IP substring length, and a suffix for a custom node name. The IP substring length specifies the number of digits at the end of a node IP address and can be used to uniquely identify a node.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports advanced security groups.|Compared with basic security groups, advanced security groups support more ECS instances, more ENIs, and effective management on an infinite number of private IP addresses. Advanced security groups are suitable in scenarios that require high O&M efficiency, high ECS instance specifications, and a large number of compute nodes. To meet the requirements of a large-scale cluster, you can select advanced security groups for **Security Group** in the advanced settings when you create the cluster.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports disk encryption and CSI.|ACK allows you to encrypt data disks. You can enable disk encryption for the selected data disks when you create a cluster. This feature can automatically encrypt the data that is transmitted from an ECS instance to a data disk and automatically decrypt the data when it is read. This improves data security. In addition, Kubernetes 1.14.6 supports the standard CSI plug-in, which is generally used for volume management. You can select FlexVolume or CSI when you create a cluster.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md), [Overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Overview.md)|

## August 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports Kubernetes 1.14.6.|ACK now supports Kubernetes 1.14.6. You can select Kubernetes 1.14.6 when you create a cluster. You cannot upgrade an existing cluster to Kubernetes 1.14.6.|All regions|[Kubernetes release notes](https://v1-14.docs.kubernetes.io/docs/setup/release/notes/)|
|AsK supports more regions.|ASK allows you to create containerized applications without managing or maintaining clusters and nodes. You are billed based on the actual amount of resources that are consumed by the elastic container instances that run the applications. ASK clusters allow you to focus on the design and development of applications, instead of managing the underlying infrastructures.

|Singapore \(Singapore\)

China \(Hong Kong\)

Australia \(Sydney\)

|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|ASK 2.0 is released and supports more native Kubernetes features|ASK 2.0 supports multiple namespaces, custom resource definitions \(CRDs\), RBAC, persistent volumes \(PVs\), and PVCs. ASK 2.0 improves the security and isolation capability of clusters. The average price of ASK clusters is reduced by 46% due to lower costs of elastic container instances. This includes a 30% reduction in CPUs and a 65% reduction in memory.|All regions|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|ACK allows you to create ACK clusters based on Super Computing Cluster \(SCC\) resources.|SCCs are powered by ECS Bare Metal \(EBM\) instances and adopt the high-speed Remote Direct Memory Access \(RDMA\) technology. SCCs improve network performance. SCCs are used in scenarios such as high-performance computing, AI, machine learning, scientific and engineering computing, data analytics, and audio and video processing. You can create SCC-based ACK clusters. This type of cluster combines high-performance infrastructure resources with lightweight and agile containers. SCC-based ACK clusters are applicable to high network throughput and compute-intensive scenarios.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK allows you to create multiple scaling groups and configure policies to schedule resources across multiple zones.|The auto scaling feature is optimized. You can configure multiple scaling groups so that resources of different specifications are automatically added when the scaling threshold is reached. This feature meets the requirements of running compute-intensive applications and GPU computing tasks. When you configure auto scaling policies, you can specify different scheduling policies for multiple zones, including priority policies, cost optimization policies, and zone balancing policies. This meets the requirement for resource scheduling when the cluster is deployed across multiple zones.|All regions|[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|ACK supports custom cluster domain names.|ACK now allows you to customize a cluster domain name by specifying the cluster-domain parameter. The cluster-domain parameter specifies the local domain name that is used for service discovery. If you have multiple clusters, we recommend that you customize the local domain names to simplify the management of clusters and services. ACK now allows you to customize a cluster domain name when you create a cluster. This simplifies management and improves the O&M efficiency.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports cloud native App Hub in App Catalog.|Cloud native App Hub provides various open source containerized applications. ACK now integrates cloud native App Hub into App Catalog. To deploy cloud native applications to your cluster, log on to the ACK console and click the **App Hub** tab on the **App Catalog** page to find and install the applications with one click. This saves you the trouble of creating clusters and deploying applications by using the command-line interface \(CLI\).|All regions|[View the application catalog](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/App catalog management/View the application catalog.md)|
|Releases a new CCM version|CCM is the core component in an ACK cluster and is responsible for managing various cloud resources, such as SLB instances and VPCs. The following features are added: -   Allows you to create SLB instances with access control settings. You can specify an IP whitelist for an SLB instance that is created by ACK. This enhances the security of the ACK cluster.
-   Allows you to specify whether to remove unschedulable nodes when you run the kubectl cordon or kubectl drain command. Cordoning and draining nodes are important features in cluster maintenance. However, the community has not reached an agreement on whether to remove a node from the backend of an SLB instance when the node is set to unschedulable for maintenance. CCM provides an interface that allows you to specify whether to remove such nodes from the backend of the SLB instance. This ensures the flexibility of maintenance.
-   Allows you to mount pods to the backend of an SLB instance through Terway. Terway ENI is the latest network plug-in that is provided by ACK. The core feature of Terway ENI is to mount the ENI IP address of a node to a pod. CCM allows you to mount pods instead of nodes to the backend of an SLB instance. This prevents traffic forwarding through nodes and improves network performance.
-   Allows you to set node weights based on the number of pods on each node for Services in Local mode. CCM can adjust the percentage of traffic that is sent to each node based on the number of pods on each node. This balances workloads among nodes. This feature applies to only Services in Local mode.

|All regions|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|

## July 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK releases the public preview of managed edge Kubernetes clusters.|Managed edge Kubernetes clusters are released for public preview. You can add edge nodes or ENS nodes to managed edge Kubernetes clusters. This type of cluster supports edge computing and manages edge nodes and ENS nodes to reduce O&M costs. This type of cluster also supports autonomous edges and networks to meet the requirements in different edge computing scenarios. You can select this type of cluster on the cluster template page.|China site \(aliyun.com\)|-|
|ACK releases the public preview of the multi-cluster management feature.|ACK supports multi-cluster management. You can select **Register Kubernetes Cluster** on the cluster template page to add Kubernetes clusters from data centers and Alibaba Cloud to the ACK console. Then, you can deploy applications to these clusters in the console. You can manage hybrid cloud clusters and clusters that are deployed across multiple clouds. After you add self-managed clusters from data centers to ACK, you can manage these clusters by using the O&M feature that is provided by ACK.|China site \(aliyun.com\)|[Register an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md)|
|ACK supports more regions for managed Kubernetes clusters on Alibaba Cloud public cloud.|Managed Kubernetes clusters are now available on the Alibaba Cloud Japan site. -   Saves resources.

You do not need to create master nodes in a managed Kubernetes cluster. If you use another type of cluster, you must create at least three master nodes.

-   Simplifies O&M.

ACK manages master nodes.

-   Ensures security.

ACK meets various security requirements.


|Japan|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK allows you to add multiple data disks to nodes when you create an ACK cluster.|ACK allows you to add multiple data disks to nodes when you create an ACK cluster. This saves you the trouble of manually adding data disks after the cluster is created. ACK formats and mounts one of the selected data disks to the docker directory. You can determine how to handle the other data disks.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK allows you to select an existing security group when you create an ACK cluster.|ACK allows you to select an existing security group when you create an ACK cluster. You can specify an existing security group for the VPC of your cluster in the advanced settings. This allows you to use custom inbound and outbound security group rules to improve the security of your cluster.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports deletion protection for clusters.|ACK supports deletion protection to ensure the security of your cluster. Currently, you are required to enter a Short Message Service \(SMS\) verification code when you delete a cluster. However, you may mistakenly delete the cluster by calling the API. To ensure the security of clusters, ACK supports deletion protection for clusters. You can enable deletion protection when you create a cluster. This way, you cannot delete the cluster in the console or by calling the API. To delete the cluster, you must first disable deletion protection. You can enable or disable deletion protection on the cluster details page.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK allows you to authorize multiple RAM users at the same time.|ACK allows you to authorize multiple RAM users at the same time and grant the permissions on all clusters. This allows you to efficiently authorize RAM users. The authorization procedure is also optimized to improve user experience.|All regions|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Overview.md)|
|ACK allows you to synchronize time zones among nodes when you create an application.|You can select **Synchronize Timezone from Node to Container** when you create an application from an image. This ensures that the application pods and the host node use the same time zone.|All regions|[Deploy a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md)|
|Container Registry Enterprise Edition supports more regions.|Container Registry Enterprise Edition is now available in the UK \(London\) region. Container Registry Enterprise Edition supports large-scale image distribution with enhanced security. This service is suitable for enterprise users that require high security and large-scale nodes.|UK \(London\)|-|
|Container Registry Enterprise Edition supports Helm charts.|Container Registry Enterprise Edition supports Helm 2 charts to make it easier for you to manage cloud native assets. You can enable the charts component on the overview page of your Enterprise Edition instance. When the component is in the running state, you can start managing Helm chart repositories.|All regions|-|

## June 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions for managed Kubernetes clusters on Alibaba Cloud public cloud.|Managed Kubernetes clusters are now available in the Japan \(Tokyo\) and UK \(London\) regions on Alibaba Cloud.|Japan \(Tokyo\)

UK \(London\)

|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|Terway supportS high-density pod deployment.|The latest version of Terway supports the One ENI per Pod or One ENI for Multi-Pod mode. The default mode is One ENI for Multi-Pod. -   One ENI per Pod: In this mode, the number of pods that can be deployed on a node must match the number of ENIs that can be created on the node. This mode improves network performance.
-   One ENI for Multi-Pod: In this mode, you can deploy multiple pods on a node. The pods share the same ENI.

|All regions|[t64408.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)|
|ACK supports Knative.|Knative is a Kubernetes-based serverless framework. Knative creates a cloud native and cross-platform orchestration standard for serverless applications. Knative implements this standard by integrating the creation of containers \(or functions\), workload management \(auto scaling\), and event models. ACK supports Knative and allows you to install and upgrade the Build, Serving, and Eventing components. Note that you must deploy Istio before you use Knative. ACK also provides instructions to deploy sample applications, and best practices of tracing, monitoring, and logging applications.|All regions|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Overview.md), [Create a Knative Service](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Manage Knative services/Create a Knative Service.md)|
|ACK allows you to search for pods by host IP or pod IP.|You can search for pods by node IP address or pod IP address. In the ACK console, choose **Applications** \> **Pods** and specify a node IP address or pod IP address to search for the pod.|All regions|-|

## May 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions on Alibaba Cloud public cloud and Alibaba Finance Cloud for managed Kubernetes clusters.|Managed Kubernetes clusters are now available in the Australia \(Sydney\) region on Alibaba Cloud public cloud and the China East 2 Finance region on Alibaba Finance Cloud. You can create managed Kubernetes clusters in the Australia \(Sydney\) region on Alibaba Cloud public cloud and the China East 2 Finance region on Alibaba Finance Cloud.

|Australia \(Sydney\)

China East 2 Finance

|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|ACK releases clusters for genomics computing.|ACK supports genomics computing clusters. This type of cluster uses high-performance computing \(HPC\) instances as worker nodes and provides a large-scale workflow engine for batch genomics computing. Genomics computing clusters are suitable for data splitting and mutation detection, and support data analytics for the following formats: BCL, FASTQ, BAM, SAM, and VCF. In the ACK console, choose **Clusters** \> **Clusters**, and click Create Kubernetes Cluster. In the Select Cluster Template dialog box, select **Genomics Computing Cluster**.|All regions|-|
|ACK releases field-programmable gate array \(FPGA\) clusters to accelerate image and video processing.|ACK supports FPGA clusters. This type of cluster uses FPGA F3 instances as worker nodes and is used for H265 video encoding and image conversion from JPEG to HEIF. FPGA-based video encoding reduces the processing time from more than one week to 15 minutes. This significantly reduces the bitrate and saves bandwidth costs when transcoding videos of the same quality. In the ACK console, choose **Clusters** \> **Clusters** and click Create Kubernetes Cluster. In the Select Cluster Template dialog box, select **Dedicated FPGA Cluster** to create a dedicated FPGA cluster.|All regions|-|
|ACK releases a new CCM version.|CCM is upgraded to V1.9.3.110-g4938309-aliyun. This version supports more SLB configuration options. The following features are provided: -   Allows you to restrict the creation of Internet-facing SLB instances by setting parameters.
-   Allows you to change certificate IDs.
-   Allows you to specify a vSwitch when you attach an internal-facing SLB instance to a Service.
-   Allows you to set SLB instance configuration to redirect traffic from HTTP port 80 to HTTPS port 443.

|All regions|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Cloud Controller Manager.md)|
|ACK upgrades Istio to V1.1.4 and supports Time Series Database \(TSDB\).|Istio 1.1.4 improves self-recovery capabilities, and supports automatic recovery of the control plane and automatic upgrade of earlier versions. Istio is also integrated with TSDB. TSDB is a database service that supports high-speed read and write operations, compressed storage, and real-time computing. To fix the local storage issues in Prometheus, TSDB provides remote storage services with high performance and high reliability at low costs.

Compared with other remote storage solutions provided by the community, TSDB is easier to use and only requires you to change the Prometheus configuration. The solution supports parallel read and write operations and is highly compatible with PromQL. TSDB is a distributed storage system with auto scaling capabilities.

|All regions|-|
|Container Registry Enterprise Edition allows you to synchronize images across all regions worldwide.|Container Registry Enterprise Edition allows you to synchronize images across all regions worldwide. This solves issues in the global delivery of applications and improves the business iteration efficiency for enterprises. Container Registry Enterprise Edition supports large-scale image distribution with enhanced security. It is suitable for enterprises that require high security and a large number of nodes.|All regions|-|
|ACK allows you to deploy a cluster across multiple zones and provides five master nodes to ensure super-high availability.|You can select multiple zones when you create a cluster. You can also configure five master nodes for a dedicated Kubernetes cluster to significantly improve the availability of the cluster.|All regions|-|

## April 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports Kubernetes 1.12.6.|Upgrades to Kubernetes 1.12.6 are available in all regions. You can upgrade managed or dedicated Kubernetes clusters from Kubernetes 1.11.5 to 1.12.6 in the ACK console.|All regions|-|
|ACK supports audit logs for managed Kubernetes clusters.|You can collect audit logs of managed Kubernetes clusters. Audit logs record operations on the API server and allow cluster administrators to trace the activities of different users.|All regions|[t21467.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Security management/Security/Use the cluster auditing feature.md)|
|Istio 1.1 supports application management in the ACK console.|ACK upgrades Istio to V1.1 and allows you to manage Istio applications in the ACK console. You can create and manage Istio applications and services on a graphical interface. You can create different application versions, implement canary releases, set canary release policies, and also configure fault injection policies. In the console, choose **Service Mesh** \> **Virtual Services** to configure the settings.

|All regions|None|
|ASK supports GPU pods.|You can create applications that run on GPU-accelerated pods in ASK clusters. When you create an application from a template, specify the pod type as GPU in the YAML file.|All regions|[t220322.md\#](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Serverless cluster best practices/Use GPU-based ECIs.md)|
|Container Registry Enterprise Edition supports more regions.|Container Registry Enterprise Edition is now available in the China \(Beijing\) region.|China \(Beijing\)| |
|ACK releases FPGA clusters to accelerate image and video processing.|ACK supports FPGA clusters. This type of cluster uses FPGA F3 instances as worker nodes and is used for H265 video encoding and image conversion from JPEG to HEIF. FPGA-based video encoding reduces the processing time from more than one week to a short period of time. This significantly reduces the bitrate and reduces bandwidth costs when transcoding videos of the same quality. In the ACK console, choose **Clusters** \> **Clusters** and click Create Kubernetes Cluster. In the Select Cluster Template dialog box, select **Dedicated FPGA Cluster** to create a dedicated FPGA cluster.|All regions|-|

## March 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions for managed Kubernetes clusters.|Managed Kubernetes clusters are now available in the China \(Zhangjiakou\), China \(Hohhot\), US \(Silicon Valley\), and Germany \(Frankfurt\) regions.|China \(Zhangjiakou\)

China \(Hohhot\)

Germany \(Frankfurt\)

US \(Silicon Valley\)

|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|Container Registry Enterprise Edition is released for public preview.|Container Registry Enterprise Edition was officially released at the Alibaba Cloud Summit on March 21, 2019. This edition provides higher security and supports large-scale image distribution. Container Registry Enterprise Edition is now in public preview in the China \(Shanghai\) region. To use the service, submit a ticket.|China \(Shanghai\)|[Introduction to Container Registry](https://www.alibabacloud.com/help/zh/doc-detail/60945.html)|
|Container Registry Shared Edition supports all regions on the Alibaba Cloud International site.|Container Registry Shared Edition is now available in all regions on the Alibaba Cloud International site \(alibabacloud.com\).|All regions|[Introduction to Container Registry](https://www.alibabacloud.com/help/zh/doc-detail/60945.html)|
|ACK supports Kubernetes 1.12.6.|ACK supports Kubernetes 1.12.6. You can create a cluster of Kubernetes 1.12 in the console.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports the Log Service plug-in for managed Kubernetes clusters.|Managed Kubernetes clusters support the Log Service plug-in. You can enable Log Service when you create a managed or dedicated Kubernetes cluster. After the plug-in is installed, you can use Log Service to manage Kubernetes logs.|All regions|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK supports more regions for managed Kubernetes clusters that run Windows.|You can now create managed ACK clusters that run Windows in the ACK console or by calling the API. This way, you can create containers that run Windows and deploy traditional Windows applications on cloud native platforms to achieve agility and elasticity.|All regions|Windows clusters are no longer supported.|
|ACK supports IPVS|ACK supports the IP Virtual Server \(IPVS\) proxy mode. Compared with the traditional iptables mode, the IPVS mode significantly improves the load balancing performance in large-scale clusters. You can use this mode in all clusters and all regions.|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports cluster templates.|ACK provides multiple cluster templates in the console. You can select templates of different cluster types based on your business requirements. Cluster templates support the following cluster types: managed Kubernetes clusters, clusters with EBM instances, GPU-accelerated clusters, and Windows clusters. Cluster templates allow you to easily and quickly create ACK clusters based on your business requirements.|All regions|-|
|ASK supports high-specification elastic container instances.|ASK clusters now support high-specification elastic container instances for genomics computing. The maximum CPU specification is increased from 8 vCPUs to 64 vCPUs. The highest specification of an elastic container instance is 64 vCPUs and 256 GiB memory. The lowest specification of an elastic container instance is 0.25 vCPU and 0.5 GiB memory. You can select a specification based on your business requirements to achieve the highest cost efficiency.|All regions|[Limits](https://www.alibabacloud.com/help/zh/doc-detail/89138.html)|

## February 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions for managed Kubernetes clusters.|Managed Kubernetes clusters provide the following core benefits: -   Saves resources. You do not need to create master nodes in a managed ACK cluster. Compared with other cluster types, this cluster type saves you the costs of three master nodes.
-   Simplifies O&M. ACK manages the master nodes.
-   Ensures security. ACK meets various security requirements.

|China \(Shenzhen\)|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK supports Knative add-ons in App Catalog.|Knative is a scale-to-zero and request-driven computing runtime based on Kubernetes and Istio. Knative supports the deployment of serverless applications and functions.

ACK provides Knative add-ons to help you build the Knative Serving environment in your cluster.

|All regions|[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Knative/Overview.md)|
|ACK supports cluster checks.|You can use this feature to perform in-depth checks on cluster resources, components, and configurations. This can identify the causes of errors in your cluster.|Mainland China|[t159904.md\#](/intl.en-US/Best Practices/Operation and maintenance/Use cluster check to troubleshoot cluster issues.md)|

## January 2019

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK releases the internal preview of containers that run Windows.|ACK now supports containers that run Windows. This allows you to deploy and run Windows applications in containers of Kubernetes clusters. This enables elastic scheduling and management of Window applications based on Kubernetes.

You can add nodes that run Windows to managed and dedicated Kubernetes clusters.

Currently, this feature is in internal preview. To use this feature, submit a ticket.

|All regions|[Create a node pool that runs Windows](/intl.en-US/User Guide for Kubernetes Clusters/Windows container/Create a node pool that runs Windows.md)|
|Container Registry Enterprise Edition is released for internal preview.|Container Registry Enterprise Edition provides container image repositories based on dedicated resources. This edition provides stable image building, large-scale image distribution, and image hosting with enterprise-class security. It is suitable for enterprises that require high security and a large number of nodes. Currently, Container Registry Enterprise Edition is in internal preview. To use the service, submit a ticket.

|All regions|-|
|ACK supports more regions for intelligent cluster O&M.|Intelligent O&M provides best practices for cluster management in different scenarios. This allows you to identify the causes of errors in the cluster by performing in-depth checks on cluster resources, components, and configurations.|China \(Hangzhou\)|[t159904.md\#](/intl.en-US/Best Practices/Operation and maintenance/Use cluster check to troubleshoot cluster issues.md)|
|ACK supports Application Real-Time Monitoring Service \(ARMS\).|ACK is now integrated with ARMS. After you install the ARMS plug-in, you can monitor the application performance in your cluster. ARMS is a monitoring service for application performance management \(APM\). To monitor a Java application, you only need to attach an ARMS agent to the startup script of the application. No code change is required. ARMS enables you to quickly locate failed API operations or slow calls, reproduce API parameters, detect memory leaks, and discover system bottlenecks. This significantly improves the efficiency of service diagnostics.

|All regions|[Monitor application performance](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Monitor application performance.md)|
|Elastic Container Instance is charged for commercial use.|On January 22, 2019, Elastic Container Instance is charged for commercial use. Elastic container instances are deployed as the underlying infrastructures of ASK cluster. You are charged when you create elastic container instances in ASK clusters. ASK clusters remain free to use.|All regions|[Billing](https://www.alibabacloud.com/help/zh/doc-detail/89142.html)|
|ASK supports more regions.|ASK clusters are now available in the China \(Beijing\) and China \(Shenzhen\) regions. ASK clusters provides extreme experience with serverless containers.|China \(Beijing\)

China \(Shenzhen\)

|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|

## December 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions|ACK is now available in all regions on the China site \(aliyun.com\) and in the UK \(London\) region on the International site \(alibabacloud.com\).|UK \(London\)|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports more regions for managed Kubernetes clusters.|Managed Kubernetes clusters are now available in the China \(Shanghai\), Malaysia \(Kuala Lumpur\), and India \(Mumbai\) regions.|China \(Shanghai\)

Malaysia \(Kuala Lumpur\)

India \(Mumbai\)

|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK allows you to remove nodes from an ACK cluster.|You can now remove nodes from an ACK cluster and decide whether to release the related ECS instances.|All regions|[Remove nodes from an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node/Remove nodes from an ACK cluster.md)|
|ACK allows you to deploy DaemonSet applications.|You can create DaemonSet applications. DaemonSet is a daemon process that ensures that all nodes run one copy of a pod.|All regions|-|
|ACK supports custom Istio gateways.|You can customize Istio Ingress and Egress gateways by configuring different parameters.|All regions|-|
|ACK supports Istio CoreDNS.|ACK now allows you to to use the CoreDNS plug-in to read Istio service entries and associate the IP addresses of the services to their host addresses.|All regions|-|
|ACK allows you to add existing ECS instances when you create managed Kubernetes clusters.|You can now add existing ECS instances as worker nodes when you create a managed Kubernetes cluster.|All regions|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|

## November 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions for managed Kubernetes clusters.|Managed Kubernetes clusters are now available in the Indonesia \(Jakarta\) region on the Alibaba Cloud International site.|Indonesia \(Jakarta\)|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|Supports Terway|ACK supports the Terway plug-in. Terway enables direct communications between containers through ENIs and providess higher network performance than Flannel.|All regions|[Work with Terway](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md)|
|ACK allows you to use thumbnail images to display the performance metrics of worker nodes.|ACK uses thumbnail images to display the performance metrics of worker nodes, which makes it easy to view the state of nodes.|All regions|-|
|ACK allows you to add multiple existing nodes at the same time.|You can now add multiple existing nodes to a cluster at the same time.|All regions|-|
|ACK supports rolling renewal of cluster certificates.|ACK now supports rolling renewal of cluster certificates to prevent certificates from expiring.|All regions|-|

## October 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions on Alibaba Finance Cloud.|ACK is now available in the China South 1 Finance region on Alibaba Finance Cloud.|China South 1 Finance|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports more regions for managed Kubernetes clusters.|-|Regions outside China|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK supports management and rollback of Deployment versions.|ACK now supports version management and rollback on Deployment applications.|All regions|-|
|ACK integrates Istio add-ons.|ACK is deeply integrated with Istio.|All regions|[Overview]()|

## September 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports Kubernetes 1.11.|-   ACK provides new features such as CRD upgrade, CoreDNS general availability \(GA\), pod priority settings, and preemptive scheduling.
-   ACK supports multiple Kubernetes versions, such as Kubernetes 1.10 and 1.11.
-   Supports multi-container applications and stateful applications in the console.

|All regions|[t21663.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateful application from an image.md)|
|ACK allows you to pull images from Container Registry private repositories without a password.|ACK now allows you to pull images from Container Registry private repositories without a password.|All regions| |
|ACK supports auto scaling of nodes.|ACK provides the auto scaling component for nodes to automatically scale in and out. Regular instances, GPU-accelerated instances, and preemptible instances can be automatically added to or removed from an ACK cluster as required. This feature is applicable to instances deployed across multiple zones and diverse instance types, and also supports different scaling modes.|All regions|[t220155.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|ACK supports preemptible instances.|-|All regions| |

## August 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK releases the public preview of managed Kubernetes clusters.|Managed Kubernetes clusters are released for public preview.|All regions|[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)|
|ACK releases Istio add-ons.|ACK releases Istio add-ons.|All regions|-|

## July 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions.|-|Australia \(Sydney\)|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports canary releases and phased releases.|-|All regions|[Overview](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Overview.md) and [Implement a phased release in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Implement a phased release in the ACK console.md)|

## June 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions.|-|Japan \(Tokyo\)

China \(Hohhot\)

|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK supports FPGA and HugePages for Kubernetes 1.10.|-|All regions|-|
|ACK supports application monitoring and alerting.|ACK supports application monitoring and alerting.|All regions|[Integration and usage with CloudMonitor]()|
|ACK supports the subscription billing method.|-|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ASK supports exec and attach commands and Ingresses.|-|All regions|[Features](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Features.md)|

## May 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports more regions on Alibaba Finance Cloud.|ACK is now available in the China East 2 Finance region on Alibaba Finance Cloud. Alibaba Finance Cloud provides services with security and compliance with security regulations.|China East 2 Finance|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ASK is released.|-|All regions|[Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)|
|ACK supports blue-green releases, canary releases, and A/B testing.|-|All regions|[Overview](/intl.en-US/Best Practices/Release/Implement a canary release and a blue/green deployment through Ingress in a Kubernetes cluster/Overview.md)|

## April 2018

|Description|Supported region|Related topic|
|-----------|----------------|-------------|
|ACK supports five regions in Southeast Asia, the Middle East, and India. The latest stable Kubernetes version is V1.9.|Malaysia \(Kuala Lumpur\)

Indonesia \(Jakarta\)

Singapore \(Singapore\)

India \(Mumbai\)

UAE \(Dubai\)

|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|
|ACK updates Service Catalog to support MySQL, RDS, RabbitMQ, and Spark.|All regions|This function is not supported|
|ACK updates App Catalog to support management of applications that are released by using Helm.|All regions|[Manage a Helm-based release](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage a Helm-based release.md)|

## March 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports Kubernetes 1.9 and custom ECS images.|ACK now supports native Kubernetes 1.9.3 and releases Workloads API. By default, CRD is enabled. GPU scheduling is supported. You can now select custom ECS images when you create a cluster. You can also reset images when you add nodes to a cluster.|All regions|-|
|ACK allows you to deploy applications by using Helm.|ACK releases App Catalog, which allows you to quickly deploy applications by using Helm.|All regions|[Manage a Helm-based release](/intl.en-US/User Guide for Kubernetes Clusters/Release management/Manage a Helm-based release.md)|
|ACK supports ServiceBroker.|ACK releases App Catalog and supports ServiceBroker.|All regions|This function is not supported|
|ACK supports node monitoring by using Cloud Monitor.|ACK now allows you to monitor nodes by using Cloud Monitor.|All regions|[Monitor basic resources](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Monitor basic resources.md)|

## January 2018

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK and Container Registry are released on the International site \(alibabacloud.com\).|-|Regions outside China|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|ACK supports Kubernetes 1.8.4 and provides features such as security enhancement and auto scaling.|-|All regions|[t220155.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|ACK releases FlexVolume to support cloud disks, NAS, and OSS.|ACK releases the FlexVolume plug-in to support cloud disks, NAS file systems, and OSS buckets.|All regions|[Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md), [t18764.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/Use NAS volumes.md), and [Use an OSS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Use an OSS volume.md)|
|ACK supports Kubernetes network policies and bandwidth throttling.|ACK now supports Kubernetes network policies and bandwidth throttling.|All regions|[Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure SLB instances.md)|
|ACK supports EBM instances.|-|All regions|-|

## October 2017

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports Kubernetes 1.8.1.|ACK now supports Kubernetes 1.8.1.|All regions|[What is Container Service for Kubernetes?](/intl.en-US/Product Introduction/What is Container Service for Kubernetes?.md)|
|ACK releases the public preview of blockchain solutions.|-|All regions|None|

## August 2017

|Feature|Description|Supported region|Related topic|
|-------|-----------|----------------|-------------|
|ACK supports Kubernetes 1.7.2.|-|All regions|[Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)|


# Release notes for Alibaba Cloud Container Service for Kubernetes {#concept_qrb_znb_yfb .concept}

This topic provides a summary of the release notes of Alibaba Cloud Container Service for Kubernetes \(ACK\).

## May 2019 {#section_67m_b42_qvo .section}

-   Released managed Kubernetes clusters in the two regions: the Sydney region of Alibaba Cloud, and the Shanghai region of Alibaba Finance Cloud. For more information, see [Types of Kubernetes clusters supported by Container Service](reseller.en-US/Product Introduction/Types of Kubernetes clusters supported by Container Service.md#) and [Create a managed Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#).
-   Released the gene computing cluster type \(a sub-type of dedicated Kubernetes clusters customized for gene computing\).

    A gene computing cluster contains the worker nodes that use high-performance ECS instances, and supports the workflow engine of high specification. It can be used to complete gene computing tasks to process format files, such as BCL, FASTQ, BAM, SAM, and VCF.

-   Upgraded Cloud Controller Manger \(CCM\) to v1.9.3.110-g4938309-aliyun that contains the following new features:
    -   Support for limiting whether Internet SLBinstances can be created by setting parameters.
    -   Support for modifying a cluster certificate ID.
    -   Support for specifying a Vswitch when a service is associated with an intranet SLB instance.
    -   Support for forwarding traffic of SLB instances from HTTP port 80 to HTTPS port 443.
-   Upgraded ACK Istio \(Istio is integrated with ACK\) to V1.1.4 with enhanced self-recovery. That is, in this version, the control plane can automatically recover, and Istio of earlier versions can automatically be upgraded.
-   Added support to allow ACK Istio to access Time Series and Spatial-Temporal Database \(TSDB\).

    Specifically, Prometheus integrated with ACK Istio supports the use of a remote storage adapter to connect to a TSDB instance. This type of instance can allow for remote storage services with high performance, stability, and reliability, at low costs.

-   Added support for automatically synchronizing images \(provided by the Enterprise Edition of Container Registry\) in all the supported regions around the globe.
-   Added support for setting multiple zones for a dedicated Kubernetes cluster and for setting five Master nodes for a dedicated Kubernetes cluster with a single zone.

## April 2019 {#section_22t_qdv_txf .section}

-   Added support for upgrading a dedicated or managed Kubernetes cluster in any regions from an earlier version to version 1.12.6 by using the Container Service console.
-   Added support for collecting audit logs for a managed Kubernetes cluster. Audit logs of a managed Kubernetes cluster record all the actions performed on the API server of the cluster. For more information, see [Kube-apiserver audit logs](../../../../reseller.en-US/User Guide/Kubernetes cluster/Security management/Kube-apiserver audit logs.md#).
-   Added support for Istio V1.1. This allows you to manage applications in the Container Service console. Specifically, you can create an application in a Kubernetes cluster in which Istio V1.1 is deployed. Then, you can create a canary version for the application, set the canary deployment policy, and set a fault injection policy. For more information, see [Deploy Istio](../../../../reseller.en-US/User Guide/Kubernetes cluster/Istio management/Deploy Istio.md#) and [Manage traffic](../../../../reseller.en-US/User Guide/Kubernetes cluster/Service mesh/Manage traffic.md#).
-   Added support for using a GPU container instance in a serverless Kubernetes cluster to create an application. Specifically, you need to specify a GPU type in the YAML file that is used to create the pod of the application. For more information, see [Use a GPU container instance](../../../../reseller.en-US/Best Practices/Serverless/Use a GPU container instance.md#).
-   Released the Enterprise Edition of Container Registry in the region of China North 2 \(Beijing\).
-   Released FPGA Kubernetes clusters that support acceleration for video and image encoding.

    An FPGA Kubernetes cluster uses the F3 instances \(a new type of FPGA instances\) as the worker nodes. Such a Kubernetes cluster allows you to encode videos with the H.265 standard and convert JPEG images to HEIF images. You can use the Container Service console to create a FPGA Kubernetes cluster.


## March 2019 {#section_phq_mrh_thb .section}

-   Added region support for managed Kubernetes clusters.

    Regions: China North 3 \(Zhangjiakou\), China North 5 \(Hohhot\), US West 1 \(Silicon Valley\), and EU Central 1 \(Frankfurt\).

-   Added region support for the Enterprise Edition of Container Registry.

    Region: China East 2 \(Shanghai\).

    **Note:** 

    -   The Enterprise Edition of Container Registry is in the beta phase. If you want to use this edition, open a ticket. For more information, see [What is Container Registry](https://www.alibabacloud.com/help/doc-detail/60945.html).
    -   In addition to the enterprise edition, ACK also provides the Shared Edition of Container Registry, which is available to all the regions in which ACK is available.
-   Upgraded the Kubernetes version used by ACK to V1.12.6.
-   Enabled the Log Service plugin to be installed for managed Kubernetes clusters.
-   Added Windows support for managed Kubernetes clusters. Clusters can be deployed Windows containers. For more information, see [Create a Kubernetes cluster that supports Windows](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster that supports Windows.md#).
-   Added the IPVS proxy mode to ACK.The IPVS proxy mode improves the load balancing performance of Kubernetes clusters that provides a large number of services.
-   Added cluster templates to the ACK console. By using a cluster template, you can more easily create customized Kubernetes clusters.
-   Increased the maximum specifications permitted of the ECI in a serverless Kubernetes cluster to that of 64 vCPU and 256GiB memory. For a detailed list of specification limits, see[Limit](https://www.alibabacloud.com/help/doc-detail/89138.html).

## February 2019 {#section_dw5_hvh_thb .section}

-   Released the managed Kubernetes clusters in China South 1 \(Shenzhen\). For more information about managed Kubernetes clusters, see [Types of Kubernetes clusters supported by Container Service](reseller.en-US/Product Introduction/Types of Kubernetes clusters supported by Container Service.md#) and [Create a managed Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#).
-   Released a Knative add-on to allow building Knative Serving environments in an ACK supported Kubernetes clusters. Knative, based on Kubernetes and Istio, provides deployment and service features for serverless applications and functions.
-   Released the intelligent cluster O&M features across all regions for Alibaba Cloud users based in Mainland China. This feature can be used to check such items as cluster resources, components, and configurations. For more information, see [Check a Kubernetes cluster to troubleshoot exceptions](../../../../reseller.en-US/Best Practices/Operation and maintenance/Check a Kubernetes cluster to troubleshoot exceptions.md#).

## January 2019 {#section_plz_3vh_thb .section}

-   Released Containerized applications in Windows \(beta version\). With this, Windows nodes can be added to dedicated or managed Kubernetes clusters.
-   Released the Enterprise edition of Container Registry \(beta version\). This edition allows you to deploy a container image repository that is built on exclusive resources, and build, distribute, and host your images in a fully secure environment with ease.
-   Released intelligent cluster O&M features in the China East 1 \(Hangzhou\) region. With this feature, you can check such items as cluster resources, components, and configurations. For more information, see [Check a Kubernetes cluster to troubleshoot exceptions](../../../../reseller.en-US/Best Practices/Operation and maintenance/Check a Kubernetes cluster to troubleshoot exceptions.md#).
-   Added Application Real-Time Monitoring Service \(ARMS\) compatibility for ACK with the ARMS plugin. By installing the ARMS plugin in your cluster, you can more can easily monitor the performance of the applications deployed in the cluster.
-   Charges introduced to Elastic Container Instance \(ECI\) used by serverless Kubernetes. Serverless Kubernetes clusters used to create container instances are charged. However, serverless Kubernetes used alone can be used free of charge. For more information, see [Billing method](https://www.alibabacloud.com/help/doc-detail/89142.html).
-   Released serverless Kubernetes clusters for Alibaba Cloud users in Mainland China.

## December 2018 {#section_zrz_jvh_thb .section}

-   Released ACK services to Alibaba Cloud in the region of UK \(London\).
-   Added region support for managed Kubernetes clusters.

    Regions: China East 2 \(Shanghai\), Asia Pacific SE 3 \(Kuala Lumpur\), and Asia Pacific SOU 1 \(Mumbai\).

-   Added feature support for nodes to be removed from Kubernetes clusters. For information on how to remove a node and whether to release the corresponding ECS instance, see [Remove a node](../../../../reseller.en-US/User Guide/Kubernetes cluster/Node management/Remove a node.md#).
-   Added function support for creating DaemonSet applications. DaemonSet guarantees that each node of a cluster has only one pod.
-   Added function support for customizing Istio gateway parameters, including ingress and egress gateways.
-   Added feature support for Istio CoreDNS.
-   Added feature support for using existing ECS instances to create a managed Kubernetes cluster is added. For more information, see [Create a managed Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#).

## November 2018 {#section_cbq_lvh_thb .section}

-   Added region support for managed Kubernetes clusters in Asia Pacific SE 5 \(Jakarta\).
-   Added the Terway network plugin. This type of network plugin provides higher performance than the Flannel network plugin. For more information, see [Terway network plugin](../../../../reseller.en-US/User Guide/Kubernetes cluster/Network management/Terway network plugin.md#).
-   Added performance metric thumbnails for each Worker node to allow for node status checks.
-   Added support to allow for multiple existing nodes to be added to a cluster at a time.
-   Added support for updating Kubernetes cluster certificates that are about to expire.

## October 2018 {#section_sld_mvh_thb .section}

-   Added region support for the Shenzhen Financial Cloud region.
-   Added the managed Kubernetes cluster type.
-   Optimized application management. Specifically, added support for managing application deployment versions, including rolling back to previous versions.
-   Added support for Istio integration with ACK. For more information, see [Overview](../../../../reseller.en-US/User Guide/Kubernetes cluster/Istio management/Overview.md#).

## September 2018 {#section_jtw_mvh_thb .section}

-   Released Alibaba Cloud Container Service for Kubernetes version 1.11.

    -   New features include CRD upgrade, CoreDNS GA, pod priority setting, and preemptive scheduling.
    -   You can configure multiple containers and create StatefulSet applications by using the Container Service console.
    For more information, see [Create a StatefulSet application by using an image](../../../../reseller.en-US/User Guide/Kubernetes cluster/Application management/Create a StatefulSet application by using an image.md#).

-   Added feature support for pulling an image without password authentication from the Container Registry.
-   Added automatic node scaling for Kubernetes clusters. For more information, see [Autoscale a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Autoscale a Kubernetes cluster.md#).
-   Released preemptible instances.

## August 2018 {#section_bbt_nvh_thb .section}

-   Added managed Kubernetes clusters \(beta version\). For more information, see [Create a managed Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a managed Kubernetes cluster.md#).
-   Released the Istio add-on plugin.

## July 2018 {#section_qk4_4vh_thb .section}

Added region support for ACK in the Australia \(Sydney\) region. Both Gray and batch releases added. For more information, see [Gray releases and blue/green deployment](../../../../reseller.en-US/Best Practices/Release/Implement a gray release and a blue__green deployment through Ingress in a Kubernetes cluster/Gray releases and blue__green deployment.md#) and [Use batch release on Alibaba Cloud Container Service for Kubernetes](../../../../reseller.en-US/User Guide/Kubernetes cluster/Release management/Use batch release on Alibaba Cloud Container Service for Kubernetes.md#).

## June 2018 {#section_bx3_pvh_thb .section}

-   Added region support forr Asia Pacific NE 1 \(Tokyo\) and China North 5 \(Hohhot\).
-   Added feature support for FPGA and HugePages in ACK 1.10.
-   Upgraded the monitoring function. Specifically, support added for customizing application monitoring and alarm notifications. For more information, see [Integration and usage with CloudMonitor](../../../../reseller.en-US/User Guide/Kubernetes cluster/Monitoring management/Integration and usage with CloudMonitor.md#).
-   Added feature support to create Subscription clusters. For more information, see [Create a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   Added new kubectl commands and the Ingress function to Serverless clusters. The kubectl commands added are the kubectl exec, kubectl attach commands. For more information, see [Kubernetes supported functions](../../../../reseller.en-US/User Guide/Serverless Kubernetes cluster/Kubernetes supported functions.md#).

## May 2018 {#section_lsy_pvh_thb .section}

-   Added region support for the Shanghai Financial Cloud region.
-   Added Serverless Kubernetes clusters. For more information, see [Serverless Kubernetes cluster](reseller.en-US/Product Introduction/Serverless Kubernetes cluster.md#).
-   Added Blue/green deployment, gray releases, and A/B testing. For more information, see [Gray releases and blue/green deployment](../../../../reseller.en-US/Best Practices/Release/Implement a gray release and a blue__green deployment through Ingress in a Kubernetes cluster/Gray releases and blue__green deployment.md#).

## April 2018 {#section_ylw_qvh_thb .section}

-   Added region support for ACK.

    Support for ACK v1.9 added in five new regions in Southeast Asia, the Middle East, and India.

-   Released Multi-zone Kubernetes clusters for high availability scenarios. These clusters deploy Master nodes in three zones, and deploy Worker nodes in multiple zones. For more information, see [Create a multi-zone Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a multi-zone Kubernetes cluster.md#).
-   Added support for MySQL, RDS, RabbitMQ, and Spark for the service catalog function. For more information, see [Overview](../../../../reseller.en-US/User Guide/Kubernetes cluster/Service catalog management/Overview.md#) of service catalog management.
-   Added support for managing applications released by using Helm. For more information, see [Manage a Heml-based release](../../../../reseller.en-US/User Guide/Kubernetes cluster/Release management/Manage a Helm-based release.md#).

## March 2018 {#section_bkn_rvh_thb .section}

-   Updated ACK to version 1.9.

    This version of ACK provides support for creating custom ECS images, CPU scheduling, enables CRD, and is compatible with workloads APIs and Kubernetes v1.9.3.

-   Released the ACK app catalog. This catelog can be used to implement one-click application deployments using Helm. For more information, see [Manage a Heml-based release](../../../../reseller.en-US/User Guide/Kubernetes cluster/Release management/Manage a Helm-based release.md#).
-   Released an ACK service catalog. This catalog supports ServiceBroker. For more information, see [Overview](../../../../reseller.en-US/User Guide/Kubernetes cluster/Service catalog management/Overview.md#) of service catalog management.
-   Upgraded the resource monitoring function of ACK. Specifically, support added for monitoring cluster nodes through CloudMonitor. For more information, see [Group-based monitoring and alarms](../../../../reseller.en-US/User Guide/Kubernetes cluster/Monitoring management/Group-based monitoring and alarms.md#).

## January 2018 {#section_f4z_rvh_thb .section}

-   Released the international versions of the websites for both Alibaba Cloud Container Registry and ACK.
-   Updated the ACK version to 1.8.4, which provides support for auto scaling and upgraded security. For more information, see [Autoscale a Kubernetes cluster](../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Autoscale a Kubernetes cluster.md#).
-   Released FlexVolume and added support for cloud disks, NAS, and OSS. For more information, see [Use Alibaba Cloud cloud disk volumes](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use Alibaba Cloud cloud disk volumes.md#), [Use NAS file systems of Alibaba Cloud](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use NAS file systems of Alibaba Cloud.md#), and [Use Alibaba Cloud OSS volumes](../../../../reseller.en-US/User Guide/Kubernetes cluster/Storage management/Use Alibaba Cloud OSS volumes.md#).
-   Upgraded the network function to support improved network policies and bandwidth limits. For more information, see [Access services by using Server Load Balancer](../../../../reseller.en-US/User Guide/Kubernetes cluster/Network management/Access services by using Server Load Balancer.md#).
-   Added ECS Bare Metal \(EBM\) instances.

## October 2017 {#section_v1t_svh_thb .section}

-   Added support for the native Kubernetes version with the ACK v1.8.1. Alibaba Cloud Container Service for Kubernetes 1.8.1 is available. For more information, see [Alibaba Cloud Container Service for Kubernetes Overview](reseller.en-US/Product Introduction/Alibaba Cloud Container Service for Kubernetes Overview.md#).
-   Released the Alibaba Cloud Container Service blockchain solution \(beta version\). For more information, see [Overview](../../../../reseller.en-US/solution/Blockchain solution/Overview.md#) of blockchain solution.

## August 2017 {#section_uwp_tvh_thb .section}

Released Alibaba Cloud Container Service for Kubernetes V1.7.2


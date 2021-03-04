---
keyword: [ESS, overview]
---

# Overview

Auto Scaling \(ESS\) is a service that can dynamically scale computing resources to meet your business requirements. This provides a more cost-efficient method to manage your resources.

## Background information

ESS is widely used in clusters of Container Service for Kubernetes \(ACK\). Typically, ESS is used in scenarios such as online workload scaling, large-scale computing and training, GPU-accelerated deep learning, inference and training based on shared GPU resources, and periodical workload scheduling. ESS defines elasticity from the following aspects:

-   Workload scaling. ESS can adjust workloads, such as pods. For example, Horizontal Pod Autoscaler \(HPA\) is a typical workload scaling component that can change the number of replicated pods to scale the workload.
-   Resource scaling. If the resources of a cluster cannot meet the requirements of the scaled workload, the related component automatically adds Elastic Compute Service \(ECS\) instances or elastic container instances \(ECIs\) to the cluster.

The components for workload scaling and resource scaling can be separately used or used in combination. If you want to decouple the components, you must scale the workload within the resource limit of the cluster.

## Scaling components for ACK clusters

![ESS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9026684161/p134711.png)

**Components for workload scaling**

|Component|Description|Scenario|Limit|Reference|
|---------|-----------|--------|-----|---------|
|HPA|Kubernetes is developed with built-in components. These components are used for online applications.|Online workloads|HPA uses Deployments and StatefulSets to scale workloads.|[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)|
|Vertical Pod Autoscaler \(VPA\)|An open source component. VPA is used for monolithic applications.|Monolithic applications|VPA is applicable to applications that cannot be horizontally scaled. In practical scenarios, VPA is used when pods are recovered from anomalies.|[Vertical pod autoscaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Vertical pod autoscaling.md)|
|CronHPA|An open source component provided by ACK. CronHPA is applicable to applications whose resource usage changes periodically.|Periodically changing workloads|CronHPA uses Deployments and StatefulSets to scale workloads. CronHPA is also compatible with HPA. You can use CronHPA and HPA in combination to scale workloads.|[CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md)|
|Elastic-Workload|A component provided by ACK. ack-kubernetes-elastic-workload is used to scale workloads with a higher level of precision. For example, it can be applied when workloads are deployed in different zones.|Workloads that require precise scaling|ack-kubernetes-elastic-workload is applicable to online workloads that require precise scaling. For example, some pods of a Deployment are scheduled to an ECS instance, and the remaining pods are scheduled to ECIs.|[Deploy and use ack-kubernetes-elastic-workload in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy and use ack-kubernetes-elastic-workload in an ACK cluster.md)|

**Components for resources scaling**

|Component|Description|Scenario|Time cost for resource deployment|Reference|
|---------|-----------|--------|---------------------------------|---------|
|cluster-autoscaler|cluster-autoscaler is an open source component provided by Kubernetes. cluster-autoscaler horizontally scales nodes in a cluster. cluster-autoscaler is integrated with ESS to provide more elastic and cost-efficient scaling services.|cluster-autoscaler is applicable to all scenarios, especially online workloads, deep learning, and large-scale computing.|The time required to add 1,000 nodes to a cluster:-   Standard mode: 120 seconds.
-   Fast mode: 60 seconds.
-   Standard mode with the Qboot firmware: 90 seconds.
-   Fast mode with the Qboot firmware: 45 seconds.

For more information about Qboot, see [Alibaba Cloud Linux 2.1903 64-bit \(Quick Start\)](/intl.en-US/Images/Alibaba Cloud Linux 2/Overview.md).


|[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|virtual-node|virtual-node is an open source component provided by ACK. virtual-node provides the runtime for serverless applications. Developers do not need to handle node resources and only need to create, manage, and pay for pods based on the actual usage.|virtual-node is used to handle traffic spikes, continuous integration \(CI\) and continuous delivery \(CD\), and big data computing.|The time required to create 1,000 pods:-   When image caching is disabled: 30 seconds.
-   When image caching is enabled: 15 seconds.

|[Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md)|
|virtual-kubelet-autoscaler|virtual-kubelet-autoscaler is a component provided by ACK. virtual-kubelet-autoscaler is used to scale serverless applications.|virtual-kubelet-autoscaler is used to handle traffic spikes, CI and CD, and big data computing.|The time required to create 1,000 pods:-   When image caching is disabled: 30 seconds.
-   When image caching is enabled: 15 seconds.

|[Install the virtual-kubelet-autoscaler add-on in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Install the virtual-kubelet-autoscaler add-on in an ACK cluster.md)|


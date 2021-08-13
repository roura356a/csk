---
keyword: [introduction to auto scaling, auto scaling component]
---

# Auto scaling overview

Auto scaling is a service that can dynamically scale computing resources to meet your business requirements. Auto scaling provides a more cost-effective method to manage your resources. This topic introduces auto scaling and the related components.

## Background

Auto scaling is widely used in Container Service for Kubernetes \(ACK\) clusters. Typically, auto scaling is used in scenarios such as online workload scaling, large-scale computing and training, GPU-accelerated deep learning, inference and training based on shared GPU resources, and periodical workload scheduling. Auto scaling enables elasticity from the following aspects:

-   Workload scaling. Auto scaling can adjust workloads, such as pods. For example, Horizontal Pod Autoscaler \(HPA\) is a typical workload scaling component that can change the number of replicated pods to scale the workload.
-   Resource scaling. If the resources of a cluster cannot meet the scaling requirements of workloads, Elastic Compute Service \(ECS\) instances or elastic container instances are added to the cluster.

The components for workload scaling and resource scaling can be used separately or in combination. If you want to decouple the components, you must scale the workload within the resource limit of the cluster.

## Scaling components for ACK clusters

![Auto scaling](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9026684161/p134711.png)

**Components for workload scaling**

|Component|Introduction|Scenario|Limit|References|
|---------|------------|--------|-----|----------|
|HPA|A built-in component of Kubernetes. HPA is mainly used for online applications.|Online business|HPA uses Deployments and StatefulSets to scale workloads.|[HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md)|
|Vertical Pod Autoscaler \(VPA\)|An open source component. VPA is mainly used for monolithic applications.|Monolithic applications|VPA is applicable to applications that cannot be horizontally scaled. In practical scenarios, VPA is used when pods are recovered from anomalies.|[Vertical pod autoscaling](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Vertical pod autoscaling.md)|
|CronHPA|An open source component provided by ACK. CronHPA is applicable to applications whose resource usage periodically changes.|Periodically changing workloads|CronHPA uses Deployments and StatefulSets to scale workloads. CronHPA is compatible with HPA. You can use CronHPA and HPA in combination to scale workloads.|[CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md)|
|Elastic-Workload|A component provided by ACK. Elastic-Workload is mainly used in scenarios where fine-grained scaling is required, for example, you want to distribute a workload to different zones.|Scenarios where fine-grained scaling is required|Elastic-Workload is applicable to online workloads that require fine-grained scaling. For example, some pods of a Deployment are scheduled to an ECS instance, and the rest of the pods are scheduled to elastic container instances.|[Install the elastic workload component](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Install the elastic workload component.md)|

**Components for resource scaling**

|Component|Description|Scenario|Time cost for delivery|References|
|---------|-----------|--------|----------------------|----------|
|cluster-autoscaler|cluster-autoscaler is an open source component provided by Kubernetes that can scale nodes in a cluster. cluster-autoscaler is integrated with auto scaling to provide more elastic and cost-effective scaling services.|cluster-autoscaler is applicable to all scenarios, especially online workloads, deep learning, and large-scale computing.|The time required to add 1,000 nodes to a cluster:-   Standard mode: 120 seconds.
-   Fast mode: 60 seconds.
-   Standard mode with images that support quick boot \(Qboot\): 90 seconds.
-   Fast mode with images that support Qboot: 45 seconds.

For more information about images that support Qboot, see [Alibaba Cloud Linux 2 \(Quick Start\)](/intl.en-US/Images/Alibaba Cloud Linux 2/Overview.md).


|[Auto scaling of nodes](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/Auto scaling of nodes.md)|
|virtual-node|virtual-node is an open source component provided by ACK. virtual-node provides the runtime for serverless applications. Developers do not need to handle node resources and only need to create, manage, and pay for pods based on the actual usage.|virtual-node is used to handle traffic spikes, continuous integration and continuous delivery \(CD/CD\), and big data computing.|The time required to create 1,000 pods in a cluster:-   When image caching is disabled: 30 seconds.
-   When image caching is enabled: 15 seconds.

|[Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md)|
|virtual-kubelet-autoscaler|virtual-kubelet-autoscaler is a component provided by ACK. virtual-kubelet-autoscaler is used to scale serverless applications.|virtual-kubelet-autoscaler is used to handle traffic spikes, CI/CD, and big data computing.|The time required to create 1,000 pods in a cluster:-   When image caching is disabled: 30 seconds.
-   When image caching is enabled: 15 seconds.

|[Install virtual-kubelet-autoscaler](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Install virtual-kubelet-autoscaler.md)|


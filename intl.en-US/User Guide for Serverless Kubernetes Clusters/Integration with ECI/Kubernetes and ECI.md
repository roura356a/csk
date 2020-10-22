# Kubernetes and ECI

Alibaba Cloud Elastic Container Instance \(ECI\) provides a basic environment for running pods of Kubernetes. However, you still need to use Kubernetes to configure features such as business dependencies, load balancing, auto scaling, and scheduling. This topic describes how to connect ECI to Kubernetes clusters and use ECI to run pods.

## Connect ECI to Kubernetes

ECI provides a hierarchical solution for Kubernetes. In this solution, ECI schedules and manages underlying pods. On the Platform as a Service \(PaaS\) layer, Kubernetes manages workloads, such as deployments, services, StatefulSets, and cron jobs.

ECI can manage the underlying infrastructure of pods and provide resources for pods as needed. In this case, Kubernetes does not need to deploy or start pods or pay attention to the resources of the underlying virtual machines \(VMs\).

You can use ECI in Kubernetes in one of the following ways:

-   Use only ECI to run pods of a Kubernetes cluster. All pods of the Kubernetes cluster are run in ECI. With ECI, you do not need to maintain underlying virtual machines \(VM\) or plan the capacity of the cluster.
-   Use both ECI and traditional servers to run pods of a Kubernetes cluster. The hybrid use of ECI and traditional servers improves the resource utilization and scaling efficiency of the Kubernetes cluster and reduces the costs of auto scaling and jobs.

## Use only ECI to run pods of a Kubernetes cluster based on Alibaba Cloud Serverless Kubernetes

You can run all pods of a Kubernetes cluster in ECI. The maintenance-free feature of ECI frees you from the maintenance work. Kubernetes only needs to manage workloads to ensure that your business applications run stably.

Alibaba Cloud Serverless Kubernetes allows you to create a serverless Kubernetes cluster built on ECI. For more information, see [Serverless Kubernetes](https://www.aliyun.com/product/ask) and [Use ECI in Serverless Kubernetes]().

We recommend that you create a serverless Kubernetes cluster in Serverless Kubernetes. Serverless Kubernetes provides a maintenance-free and cost-effective Kubernetes environment for you to run online or offline business applications and perform simulation, development, and testing.

## Use both ECI and traditional servers to run pods of a Kubernetes cluster

If you have created a Kubernetes cluster, you can connect ECI to the cluster through virtual nodes. This improves resource utilization and scaling efficiency of the Kubernetes cluster and reduces operating costs. When the traffic of some long-running workloads increases, you can schedule the traffic to ECI, which shortens the time required to perform a scale-out, reduces the scale-out costs, and improves the utilization rate of existing resources.

When the traffic decreases, the Kubernetes cluster can release the pods deployed in ECI to reduce costs.

You can connect ECI to an existing Kubernetes cluster in one of the following ways based on the type of the cluster:

-   To connect ECI to a managed Kubernetes cluster in Alibaba Cloud Container Service for Kubernetes, add virtual nodes in the Container Service console. For more information, see [Managed Kubernetes](https://www.aliyun.com/product/kubernetes) and [Use ECI in Container Service for Kubernetes]().

-   To connect ECI to a Kubernetes cluster created on Elastic Compute Service \(ECS\) instances, install Virtual Kubelet in the cluster. For more information, see [Use ECI in a user-created Kubernetes cluster through Virtual Kubelet]().
-   To connect ECI to a Kubernetes cluster deployed in an on-premises data center or other clouds, install Virtual Kubelet in the cluster. Virtual Kubelet allows you to use the scalable resources of Alibaba Cloud in the Kubernetes cluster. For more information, contact your Alibaba Cloud architects.


Connect Kubernetes to Elastic Container Instance 
=====================================================================

Elastic Container Instance can provide a basic runtime environment of pods for Kubernetes clusters. Other capabilities such as dependencies between business, load balancing, auto scaling, and regular scheduling must still be provided by Kubernetes. This topic describes how to connect Kubernetes to Elastic Container Instance and use Elastic Container Instance to run pods.

Overview 
-----------------------------

Elastic Container Instance provides a hierarchical solution for Kubernetes. In this solution, Elastic Container Instance schedules and manages underlying pods. On the Platform as a Service (PaaS) layer, Kubernetes manages workloads such as Deployments, Services, StatefulSets, and CronJobs.

Elastic Container Instance can manage the underlying infrastructure of pods and provide resources for pods. In this scenario, Kubernetes does not need to deploy or start pods or pay attention to the resources of the underlying virtual machines (VMs).

Elastic Container Instance seamlessly connects to Kubernetes by using virtual nodes based on Virtual Kubelet provided by the Kubernetes community. In this scenario, Kubernetes clusters can obtain high elasticity without being limited by the computing capacities of cluster nodes. For more information, visit [Virtual Kubelet](https://virtual-kubelet.io/docs/architecture/?spm=a2c4g.11186623.2.14.333d1d62vcFRYN).

You can use one of the following methods to connect Elastic Container Instance to Kubernetes: 



* Run pods of Kubernetes clusters only on Elastic Container Instance

  When you deploy a serverless Kubernetes (ASK) cluster, the entire ASK cluster runs on Elastic Container Instance, which eliminates the need to focus on O\&M and capacity issues of the underlying VMs.
  

* Run pods of Kubernetes clusters on both Elastic Container Instance and traditional servers

  Elastic Container Instance connects to Kubernetes clusters as virtual nodes, which can improve the resource usage and elasticity of clusters and reduce running costs.
  




Run pods of Kubernetes clusters only on Elastic Container Instance (ASK) 
---------------------------------------------------------------------------------------------

You can run all pods of a Kubernetes cluster in Elastic Container Instance, which frees you from the maintenance work. Kubernetes needs only to manage workloads to ensure that your business runs stably.

If you are selecting a Kubernetes cluster, we recommend that you select an ASK cluster. ASK can provide you with Kubernetes clusters that run entirely on Elastic Container Instance. It also provides an O\&M-free and cost-effective Kubernetes environment for your online and offline business, simulation environments, and development and test environments.

For more information about how to use Elastic Container Instance in ASK clusters, see [Use Elastic Container Instance in ASK clusters]().

Run pods of Kubernetes clusters on both Elastic Container Instance and traditional servers 
---------------------------------------------------------------------------------------------------------------

If you have an existing Kubernetes cluster, you can add Elastic Container Instance as a virtual node to the cluster. You can schedule elastic traffic of long-running business loads to Elastic Container Instance to reduce scaling time and costs and make full use of existing resources. When business traffic decreases, the Kubernetes cluster can release the pods deployed in Elastic Container Instance to reduce costs.

You can use one of the following methods to connect to Elastic Container Instance based on your existing Kubernetes clusters:

* If you use Container Service for Kubernetes (ACK) to deploy clusters, you can add virtual nodes in the ACK console to use Elastic Container Instance. For more information, see [Use Elastic Container Instance in ACK clusters]().

  

* If you create Kubernetes clusters in Elastic Compute Service (ECS), you must deploy Virtual Kubelet to use Elastic Container Instance. For more information, see [Use Elastic Container Instance in self-managed Kubernetes clusters]().

  

* If you deploy Kubernetes clusters in data centers or other cloud platforms, you must deploy Virtual Kubelet to use Elastic Container Instance. For more information, see [Use Elastic Container Instance in offline Kubernetes clusters](). For further help, consult solution architects of Alibaba Cloud.

  




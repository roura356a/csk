---
keyword: [features of ASK cluster, ASK cluster]
---

# Features

This topic describes the features provided by serverless Kubernetes \(ASK\) clusters. You can use ASK clusters more efficiently after you read and understand the features.

## Virtual nodes

In ASK clusters, pods are created based on virtual nodes. Virtual nodes enable seamless integration between Kubernetes and Elastic Container Instance, and empower Kubernetes clusters with high elasticity. This way, Kubernetes clusters are no longer limited by the computing capacity of underlying resources.

![ask](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4197297951/p86903.png)

You can view information about virtual nodes in your clusters. Virtual nodes do not occupy computing resources and do not require manual operations for management. For more information about virtual nodes, see [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

## Pod isolation

Each pod in an ASK cluster runs in a secure and isolated container runtime built on an elastic container instance. The underlying computing resources of different elastic container instances are completely isolated by lightweight virtual sandboxes. Elastic container instances do not affect each other.

The underlying computing resources of elastic container instances run Alibaba Cloud Linux2. ASK clusters provide serverless container services. You cannot access the underlying operating system of elastic container instances.

## Pod configurations

Pods are created based on elastic container instances and support multiple Kubernetes-native features, including multiple container startup, environment variables, restart policies, health check commands, volume mounting, and pre-stop commands. You can run the `kubectl logs` command to view log data of containers and run the `kubectl exec` command to manage containers.

ASK clusters support various annotations to extend the features of pods. For more information, see [Elastic Container Instance overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod/Elastic Container Instance overview.md).

## Workloads

-   ASK clusters support Kubernetes-native workloads such as Deployments, StatefulSets, Jobs, CronJobs, pods, and CustomResourceDefinitions \(CRDs\).
-   ASK clusters do not support DaemonSets because ASK clusters do not support the features that are related to nodes.

## Auto scaling

ASK clusters do not contain real nodes. You do not need to be concerned about node capacity planning or cluster expansion by using cluster-autoscaler. You only need to scale applications to meet your business requirements. We recommend that you configure Horizontal Pod Autoscaler \(HPA\) or CronHPA policies to adjust the number of pods to meet your business requirements.

## Network management

By default, Elastic Container Instance-based pods use the host network mode. Each pod must be assigned an elastic network interface \(ENI\) by the vSwitch. This enables communications with the Elastic Compute Service \(ECS\) instances and Relational Database Service \(RDS\) instances in the virtual private cloud \(VPC\) where the cluster is deployed.

-   **Service**
    -   You can create LoadBalancer Services.
    -   You cannot create NodePort Services because ASK clusters do not support the features that are related to nodes.
-   **Ingress**
    -   SLB Ingress: supports Layer-7 traffic forwarding based on Server Load Balancer \(SLB\) instances without controllers. For more information, see [ingress demo](https://github.com/AliyunContainerService/serverless-k8s-examples/tree/master/ingress-alb).
    -   NGINX Ingress: allows you to create NGINX Ingresses after nginx-ingress-controller is deployed. For more information, see [ingress-nginx demo](https://github.com/AliyunContainerService/serverless-k8s-examples/tree/master/ingress-nginx).
-   **Service discovery**

    To use the service discovery feature within a cluster, enable PrivateZone when you create the cluster.

-   **EIP**

    You can associate elastic IP addresses \(EIPs\) with Elastic Container Instance-based pods. You can automatically create an EIP for an Elastic Container Instance-based pod or associate an existing EIP with an Elastic Container Instance-based pod.


## Storage management

You can mount Alibaba Cloud disks or Apsara File Storage NAS \(NAS\) file systems to pods.

-   Alibaba Cloud Disks
    -   To mount a disk through FlexVolume, you do not need to install FlexVolume. You can mount a disk by specifying the disk ID. For more information, see [disk-flexvolume-static.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/disk-flexvolume-static.yaml). You can also dynamically provision disks. For more information, see [disk-flexvolume-dynamic.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/disk-flexvolume-dynamic.yaml).
    -   To dynamically provision disks by creating persistent volumes \(PVs\) and persistent volume claims \(PVCs\), you must first install disk-controller. For more information, see [disk-pvc-dynamic.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/disk-pvc-dynamic.yaml).
-   NAS file systems
    -   To use NFS volumes, you can mount NAS file systems through NFS. For more information, see [nas-nfsvolume.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/nas-nfsvolume.yaml).
    -   To statically mount NAS file systems through FlexVolume, you can directly specify the mount target without the need to install FlexVolume. For more information, see [nas-flexvolume.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/nas-flexvolume.yaml).
    -   To statically mount NAS file systems by creating PVs and PVCs, you must first install disk-controller. For more information, see [nas-pvc.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/nas-pvc.yaml).

## Log management

In ASK clusters, stdout outputs and text outputs are collected from pods without the need to deploy a Logtail DaemonSet. For more information, see [Collect log files by using Log Service](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Log management/Collect log files by using Log Service.md).

## ConfigMaps and Secrets

ASK clusters support ConfigMaps and Secrets. You can mount ConfigMaps and Secrets as volumes.

## Chart management

ASK clusters support chart deployment in App Catalog to create various Kubernetes-native applications.


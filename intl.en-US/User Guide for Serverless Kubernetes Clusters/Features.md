---
keyword: [features of ASK cluster, ASK cluster]
---

# Features

This topic describes the features provided by serverless Kubernetes \(ASK\) clusters. For more efficient management on ASK clusters, we recommend that you first read and understand this topic.

## Virtual nodes

In ASK clusters, pods are deployed on virtual nodes. Virtual nodes enable seamless integration between Kubernetes clusters and elastic container instances \(ECIs\). Virtual nodes eliminate the limits of underlying computing resources and provide great elasticity for your clusters.

![ask](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4197297951/p86903.png)

You can view information about virtual nodes in your clusters. Virtual nodes do not occupy computing resources and do not require manual operations for management. For more information about virtual nodes, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).

## Pod isolation

Each pod in an ASK cluster runs in a secure and isolated container runtime built on an ECI. The underlying compute resources of different ECIs are completely isolated by lightweight virtual sandboxes. ECIs do not affect each other.

The underlying compute resources of ECIs run Alibaba Cloud Linux2. ASK clusters serve as serverless container services. You cannot access the underlying operating system of ECIs.

## Pod configurations

Pods are created based on ECIs and support multiple native Kubernetes features, including multiple container startup, environment variables, restart policies, health check commands, volume mounting, and pre-stop commands. You can run the `kubectl logs` command to view container logs and run the `kubectl exec` command to manage containers.

ASK clusters support various annotations to extend the features of pods. For more information, see [ECI overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod management/ECI overview.md).

## Workloads

-   ASK clusters support native Kubernetes workloads such as Deployments, StatefulSets, Jobs, CronJobs, pods, and CustomResourceDefinitions \(CRDs\).
-   ASK clusters do not support DaemonSets because ASK clusters do not support the features that are related to nodes.

## Auto scaling

ASK clusters do not contain real nodes. You do not need to be concerned about node capacity planning or cluster expansion by using cluster-autoscaler. You only need to scale your application based on your business requirements. We recommend that you configure Horizontal Pod Autoscaler \(HPA\) or CronHPA policies to adjust the number of pods based on your business requirements.

## Network management

By default, pods use the host network mode. Each pod must be assigned an elastic network interface \(ENI\) by the VSwitch to enable communications with the Elastic Compute Service \(ECS\) instances and Relational Database Service \(RDS\) instances in the virtual private cloud \(VPC\) where the cluster is deployed.

-   **Service**
    -   You can create LoadBalancer type Services.
    -   You cannot create NodePort type Services because ASK clusters do not support the features that are related to nodes.
-   **Ingress**
    -   SLB Ingress: allows you to forward traffic at Layer 7 based on Server Load Balancer \(SLB\) instances without deploying controllers. For more information, see [ingress demo](https://github.com/AliyunContainerService/serverless-k8s-examples/tree/master/ingress-alb).
    -   NGINX Ingress: allows you to create NGINX Ingresses after nginx-ingress-controller is deployed. For more information, see [ingress-nginx demo](https://github.com/AliyunContainerService/serverless-k8s-examples/tree/master/ingress-nginx).
-   **Service discovery**

    To use the service discovery feature within a cluster, enable PrivateZone when you create the cluster.

-   **EIP**

    You can bind elastic IP addresses \(EIPs\) to ECI pods. You can enable an ECI pod to automatically create an EIP or bind an existing EIP to the ECI pod.


## Storage management

You can mount disks of Alibaba Cloud or Network Attached Storage \(NAS\) file systems provided by Apsara File Storage NAS to pods.

-   Disks of Alibaba Cloud
    -   To mount a disk through FlexVolume, you do not need to install FlexVolume. You can mount a disk of Alibaba Cloud by specifying the disk ID. For more information, see [disk-flexvolume-static.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/disk-flexvolume-static.yaml). You can also dynamically mount disks. For more information, see [disk-flexvolume-dynamic.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/disk-flexvolume-dynamic.yaml).
    -   To dynamically mount disks through persistent volumes \(PVs\) and persistent volume claims \(PVCs\), you must first install disk-controller. For more information, see [disk-pvc-dynamic.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/disk-pvc-dynamic.yaml).
-   NAS file systems of Apsara File Storage NAS
    -   To mount NAS file systems through Network File System \(NFS\), see [nas-nfsvolume.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/nas-nfsvolume.yaml).
    -   To statically mount NAS file systems through FlexVolume, you can directly specify the mount target without the need to install FlexVolume. For more information, see [nas-flexvolume.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/nas-flexvolume.yaml).
    -   To statically mount NAS file systems through PVs and PVCs, you must first install disk-controller. For more information, see [nas-pvc.yaml demo](https://github.com/AliyunContainerService/serverless-k8s-examples/blob/master/volumes/nas-pvc.yaml).

## Log management

In ASK clusters, stdout logs and text logs are collected from pods without the need to deploy Logtail as a DaemonSet. For more information, see [Collect logs through Log Service](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Log management/Collect logs through Log Service.md).

## ConfigMaps and Secrets

Supports ConfigMaps and Secrets. You can mount ConfigMaps and Secrets as volumes.

## Chart management

Supports chart deployment in App Catalog to create various native Kubernetes applications.


Migrate existing Kubernetes applications to ECI 
====================================================================



You can use Elastic Container Instance (ECI) with [Alibaba Cloud Serverless Kubernetes]() and [Alibaba Cloud Container Service for Kubernetes](). This topic describes the adjustment required for migrating existing applications to ECI, and the issues that you may encounter during the migration.

Prerequisites 
----------------------------------

You understand basic concepts of Kubernetes or have used a Kubernetes-based container orchestration service on a private or public cloud before.

Preparations 
---------------------------------

* Learn about the process of using ECI in Kubernetes. For more information, see [Use ECI in Serverless Kubernetes]() and [Use ECI in Container Service for Kubernetes]().

  

* You do not need to create an ECI in advance. You only need to create a serverless Kubernetes cluster or install the ack-virtual-node add-on in an existing managed Kubernetes cluster.

  




Manage Kubernetes clusters and ECIs 
--------------------------------------------------------

* You can use the [Container Service console](https://cs.console.aliyun.com/) to manage serverless Kubernetes clusters and managed Kubernetes clusters.

  

* You can use Alibaba Cloud Cloud Shell to manage Kubernetes clusters. For more information, see [Use kubectl on Cloud Shell to manage Kubernetes clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).

  

* You can use the kubectl client to connect to a remote Kubernetes cluster from your local device. For more information, see [Connect to Kubernetes clusters through kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

  




View created ECIs 
--------------------------------------

* Log on to the [ECI console](https://eci.console.aliyun.com). In the upper-left corner, select the target region from the drop-down list. The created ECIs appear. If a blank page appears, you need to apply for the permission to view ECIs.

  

* Log on to the [Container Service console](https://cs.console.aliyun.com/). In the left-side navigation pane, choose **Applications** \> **Pods** . On the page that appears, select the target cluster and namespace from the drop-down lists in the upper-left corner. The created pods appear. Pods that are scheduled to the virtual-kubelet node are ECIs. Click **Details** in the Actions column of an ECI to view its details.

  




![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7149930951/p110846.png)

Limits on application migration 
----------------------------------------------------

ECIs are not scheduled to the virtual-kubelet node in a **centralized** manner because this node is a virtual node used by ECI to interact with Kubernetes. Instead, ECIs are scattered in the whole resource pool of Alibaba Cloud.

Due to security issues of the Internet and limits of a virtual node, ECI does not support host-related features and DaemonSets. The following table lists the features that are not supported by ECI currently.


|           Feature            |                          Description                          |                         Alternative                          |
|------------------------------|---------------------------------------------------------------|--------------------------------------------------------------|
| HostPath                     | Allows you to mount a file from the host to a container.      | Use an emptyDir volume or Apsara File Storage NAS.           |
| HostNetwork                  | Allows you to map a host port to a container.                 | Create services of the LoadBalancer type.                    |
| DaemonSet                    | Allows you to deploy a static pod on the host of a container. | Deploy multiple images in a pod by using sidecar containers. |
| Privileged permissions       | Allows you to grant privileged permissions to a container.    | Use securityContext to grant permissions to a pod.           |
| Service of the NodePort type | Allows you to map a host port to a container.                 | Create services of the LoadBalancer type.                    |



Remarks on application migration 
-----------------------------------------------------

* Serverless Kubernetes can share an image repository with Kubernetes clusters. You can upload your container images to this image repository in advance. We recommend that you use VPC addresses of images to accelerate image pulling, which are in the format of registry-vpc.xxx.

  

* Serverless Kubernetes clusters and virtual nodes support common controllers such as the deployment, ReplicaSet, job, CronJob, and StatefulSet controllers. Theoretically, applications of these types can run directly in Serverless Kubernetes clusters and on virtual nodes

  

* Serverless Kubernetes clusters and virtual nodes use PrivateZone to expose services. We recommend that you enable the PrivateZone service for a serverless Kubernetes cluster when you create the cluster.

  

* Serverless Kubernetes clusters and virtual nodes support services of the LoadBalancer type. You can change the type field of a service to LoadBalancer to migrate the service.

  




    apiVersion: v1
    kind: Service
    metadata:
      labels:
        app: nginx
      name: nginx
      namespace: default
    spec:
      externalTrafficPolicy: Cluster
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        app: nginx
      sessionAffinity: None
      type: LoadBalancer



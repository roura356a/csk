Migrate existing Kubernetes applications to Elastic Container Instance 
===========================================================================================

This topic describes how to use Elastic Container Instance by using serverless Kubernetes (ASK) or by combining Kubernetes clusters with virtual nodes. This topic can help you understand the adaptations and transformations required to migrate applications to Elastic Container Instance.

Preparations 
---------------------------------

1. Understand the basic principles of connecting Kubernetes to Elastic Container Instance. For more information, see [Connect Kubernetes to Elastic Container Instance]().

   

2. Based on how you use Elastic Container Instance, create an ASK cluster or deploy a virtual node in an existing Kubernetes cluster.

   




Management tools 
-------------------------------------

You can use the following methods to manage Kubernetes and the running status of elastic container instances:

* Elastic Container Instance console

  You can check the running status of elastic container instances in the Elastic Container Instance console. Perform the following steps:
  1. Log on to the [Elastic Container Instance console](https://eci.console.aliyun.com).

     
  
  2. In the top navigation bar, select a region.

     
  
  3. On the **Container Group** page, view the created elastic container instances in the selected region.

     
  

  

* Container Service for Kubernetes (ACK) console

  You can manage ASK or ACK clusters and view the running status of elastic container instances in the ACK console. To view the running status of elastic container instances, perform the following steps:
  1. Log on to the [ACK console](https://cs.console.aliyun.com/).

     
  
  2. In the left-side navigation pane, click **Clusters** .

     
  
  3. In the cluster list, find the cluster that you want to view and click the cluster ID to go the details page.

     
  
  4. In the left-side navigation pane, choose **Workloads \> Pods** .

     
  
  5. On the **Pods** page, select a namespace from the drop-down list. Then, you can view elastic container instances of the namespace.

     
  

  

* Alibaba Cloud Shell

  You can manage Kubernetes clusters by using Cloud Shell provided by Alibaba Cloud. For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).
  

* kubectl client

  You can use the kubectl client to connect to a remote Kubernetes cluster from your computer. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
  




Limits 
---------------------------

Elastic Container Instance does not support some Kubernetes features such as hostPaths and DaemonSets due to security limits of Alibaba Cloud public cloud and limits imposed by virtual nodes. The following table describes the unsupported features.


|     Unsupported feature      |                           Description                           |                   Recommended alternative                    |
|------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------|
| hostPath                     | Allows you to mount files from on-premises hosts to containers. | Use an emptyDir volume or Apsara File Storage NAS (NAS).     |
| hostNetwork                  | Allows you to map a host port to a container.                   | Create a Service of the LoadBalancer type.                   |
| DaemonSet                    | Allows you to deploy a static pod on the host of a container.   | Deploy multiple images in a pod by using sidecar containers. |
| Privileged permissions       | Allows you to grant privileged permissions to a container.      | Use a security context to grant permissions to a pod.        |
| Service of the NodePort type | Allows you to map a host port to a container.                   | Create a Service of the LoadBalancer type.                   |



Notes 
--------------------------

* ASK clusters share an image repository with Kubernetes clusters. To simplify image pulling, you can upload your container images to the image repository in advance. We recommend that you use the VPC-type image address such as registry-vpc.xxx.

  

* ASK clusters and the combination of Kubernetes clusters and virtual nodes support common controllers such as Deployments, ReplicaSets, Jobs, CronJobs, and StatefulSets. Applications of these types can run directly in ASK and Kubernetes clusters.

  

* ASK clusters and the combination of Kubernetes clusters and virtual nodes use PrivateZone to conduct service discovery. We recommend that you enable PrivateZone when you create a cluster.

  

* ASK clusters and the combination of Kubernetes clusters and virtual nodes support loading balancing, which indicates that the type of Service is LoadBalancer. For more information, see [Configure SLB instances](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).

  YAML example:

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

  




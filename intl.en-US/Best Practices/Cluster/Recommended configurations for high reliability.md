# Recommended configurations for high reliability

This topic describes the recommended configurations for creating a Container Service for Kubernetes \(ACK\) cluster where applications can run stably and reliably.

## Disk type and size

Disk type

-   We recommend that you select an SSD.
-   When you create an ACK cluster, select **Mount Data Disk** for worker nodes. The data disk is provided to /var/lib/docker for storing local images in case the storage of the root disk is exhausted when the number of images increases. After a time period, the disk may contain images that are no longer needed. To clear these images, bring the machine offline, recreate a data disk, and then bring the machine online again.

Disk size

Docker images, system log, and application log are all stored in disks. Therefore, Kubernetes nodes require large disk space. When you create an ACK cluster, you must take into consideration the number of pods that you want to deploy on each node, the log size of each pod, the image size, the temporary data size, and the space reserved for the system.

We recommend that you reserve 8 GB of disk space for the operating system running on Elastic Compute Service \(ECS\) instances. The operating system occupies about 3 GB of disk space. The remaining space is used by Kubernetes resource objects.

## Whether to create worker nodes when you create an ACK cluster

When you create an ACK cluster, you can set **Node Type** to the following values:

-   **Pay-As-You-Go**: creates worker nodes when you create the ACK cluster.
-   **Subscription**: does not create worker nodes when you create the ACK cluster. After the cluster is created, you can purchase ECS instances and add them to the cluster based on your requirements.

## Network selection

-   If you want to connect your cluster to external services such as ApsaraDB RDS for MySQL \(RDS\), you must use an existing virtual private cloud \(VPC\) instead of creating a VPC. VPCs are isolated from each other. You can create a VSwitch and attach the ECS instances of your cluster to the VSwitch. This facilitates cluster management.
-   You can select one of the following network plug-ins when creating an ACK cluster: Terway and Flannel. For more information, see [t64408.md\#section\_k1z\_f1n\_lmh](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/Work with Terway.md).
-   Do not set a small CIDR block for the pod network. Otherwise, the number of supported nodes is limited. To set this parameter, consider the value that you want to specify for the **Pod Number for Node** parameter in the **Advanced Settings** section. For example, if you set the CIDR block of the pod network to X.X.X.X/16, it indicates that 256 × 256 IP addresses are assigned to your cluster. If the Pod Number for Node parameter is set to 128, the maximum number of nodes supported by your cluster is 512.

## Use cross-zone deployment

Alibaba Cloud supports cross-region deployment and each region has one or more zones. Zones are isolated locations in a region. The power supply and network of each zone are independent. Using cross-zone deployment enables cross-zone disaster recovery, but increases network latency.

## Claim resources of each pod

In an ACK cluster, excessive pods may be scheduled to one node. This overloads the node and the node can no longer provide services.

To avoid this issue, you can specify the requests parameter and the limits parameter when you deploy a pod in your cluster. This ensures that the pod is deployed on a node with sufficient resources. In the following example, the NGINX pod requires 1 CPU core and 1,024 MiB of memory. When the pod is running, the upper limit of resource usage is 2 CPU cores and 4,096 MiB of memory.

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    resources: # Resource claim
      requests:
        memory: "1024Mi"
        cpu: "1000m"
      limits:
        memory: "4096Mi"
        cpu: "2000m"
```

ACK uses a static resource scheduling method and calculates the remaining resources on each node in the following way: `Remaining resources = Total resources - Allocated resources`. The allocated resources are not equivalent to the resources that are used. When you manually run a resource-consuming program, ACK does not deduct the resources used by the program.

You must claim resources for all pods. For a pod that does not have resource claims, after it is scheduled to a node, the resources used by the pod are not deducted from the total resources of the node. As a result, excessive pods may be scheduled to this node.

## Routine O&M

-   Log

    When you create an ACK cluster, select **Enable Log Service**.

-   Monitoring

    ACK integrates the Cloud Monitor service of Alibaba Cloud. You can dynamically monitor your cluster by configuring node monitoring. You can add alert rules to help you locate the cause of unexpected high resource usage on nodes.

    When you create an ACK cluster, two application groups are automatically created in the Cloud Monitor console: one for the master nodes of your cluster and the other for the worker nodes. You can add alert rules to these application groups. The rules apply to all nodes in the application groups. New nodes of the cluster are automatically classified into the application groups. You do not need to create alert rules for the new nodes again.

    ![Alert rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1635359951/p14010.png)

    Therefore, you only need to configure alert rules for ECS instances.

    **Note:**

    To monitor ECS instances, set alert rules for the **cpu**, **memory**, and **disk** resources in routine O&M. We recommend that you store /var/lib/docker in a separate disk.

    ![Basic information](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1635359951/p14012.png)


## Waiting dependencies ready instead of terminating an application during startup

Some applications may have external dependencies. For example, an application may need to read data from a database or call the interface of another service. However, when the application starts, the database or the interface may be unready. In manual O&M, the application is terminated when the external dependencies are unready. This is known as failfast. This strategy is not suitable for ACK clusters. Most O&M activities in ACK are automated. You do not need to manually deploy an application, start the application on a selected node, or restart the application when it fails. Applications in ACK clusters are automatically restarted upon failures. You can also scale the number of pods through Horizontal Pod Autoscaler \(HPA\) to deal with increased loads.

For example, Application A is reliant on Application B and both applications run on the same node. The node restarts due to some reason. After the node is restarted, Application A is started while Application B is not. In this case, the dependency of Application A is unready. The traditional way terminates Application A in this case. After Application B is started, Application A must be manually started.

The best way for ACK is to check whether the dependencies are ready in a round robin manner and wait until all dependencies are ready instead of terminating the application. This can be achieved by using [Init Container](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/#what-can-init-containers-be-used-for).

## Configure the restart policy

Application processes running in a pod may be closed due to bugs in the code or excessive memory use. The pod fails when the processes are closed. You can set the restartPolicy parameter for the pod. This ensures that the pod can be automatically restarted upon failures.

```
apiVersion: v1
kind: Pod
metadata:
  name: tomcat
spec:
  containers:
  - name: tomcat
    image: tomcat
    restartPolicy: OnFailure # 
```

Valid values of the restartPolicy parameter:

-   Always: automatically restarts the pod in all cases.
-   OnFailure: automatically restarts the pod upon failures \(the state of the closed process is not 0\).
-   Never: never restarts the pod.

## Configure liveness probes and readiness probes

A pod may be unable to provide services even if the pod is in the Running state. The processes in a running pod may be locked. Consequently, the pod cannot provide services. However, Kubernetes does not restart such a pod because the pod is still running. Therefore, you must configure liveness probes for all pods in a cluster. The probes check whether the pods are alive and can provide services. When a liveness probe detects exceptions in a pod, the pod is automatically restarted.

A readiness probe is used to determine whether a pod is ready to provide services. It takes some time for an application to initialize during startup. During the initialization, the pod where the application runs cannot provide services. A readiness probe is used to inform Ingresses or Services whether the pod is ready to receive network traffic. When pod errors are detected by readiness probes, Kubernetes stops forwarding network traffic to the pod.

```
apiVersion: v1
kind: Pod
metadata:
  name: tomcat
spec:
  containers:
  - name: tomcat
    image: tomcat
    livenessProbe:
      httpGet:
        path: /index.jsp
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 3
    readinessProbe:
      httpGet:
        path: /index.jsp
        port: 8080
```

## Run only one process in each container

Users who are new to the container service tend to use containers as virtual machines \(VMs\) and run multiple processes in one container. The processes include the monitoring process, logging process, sshd process, and the systemd. This causes the following two issues:

-   It becomes complex to determine the resource usage of a pod. Implementing resource limit also becomes difficult.
-   Running only one process in each container ensures that the container engine can detect process failures and restart the container upon each failure. If a container contains multiple processes, the container may be not affected when one of the processes is terminated. The external container engine is unaware of the terminated process and therefore does not perform any action on the container. However, the container may not be able to function as expected in this case.

ACK allows you to run multiple processes together. For example, if you want NGINX and php-fpm to communicate with each other by using a UNIX domain socket, you can use a pod that contains two containers. Then, put the UNIX domain socket into a volume shared by the two containers.

## Eliminate single points of failure \(SPOF\)

If an application uses only one ECS instance, the application becomes unavailable during the time period in which the ECS instance is restarted upon a failure. The application also becomes unavailable when it is upgraded or when a new version is released. Therefore, we recommend that you do not directly run applications in pods. Instead, deploy applications by using Deployments or StatefulSets and use more than two pods for each application.


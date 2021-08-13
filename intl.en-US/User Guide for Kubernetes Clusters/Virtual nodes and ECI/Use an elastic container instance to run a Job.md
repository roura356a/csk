---
keyword: [virtual node, Job]
---

# Use an elastic container instance to run a Job

This topic describes how to run a Job by using a virtual node. This method minimizes cluster O&M costs because you do not need to create new nodes in your cluster to handle the peak load.

Pods cannot run as normal when the nodes in a cluster have insufficient computing resources. However, creating a large number of nodes in the cluster may result in resource wastes. A virtual node offers near-unlimited computing resources for a Kubernetes cluster and avoids resource wastes, such as idle nodes. It allows you to create pods in a cluster based on your business requirements and ensures that the cluster can handle fluctuations in resource demands. A Kubernetes cluster that uses a large number of virtual nodes has the following characteristics:

-   Workloads in the cluster have obvious peak hours and off-peak hours.
-   The cluster uses resource pools that consist of elastic container instances to handle traffic spikes and run Jobs. This reduces computing costs to the maximum extent.

![Kubernetes clusters](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3538627261/p49860.png)

In this example, a managed Kubernetes cluster that contains two worker nodes is created. The master nodes are managed by Container Service for Kubernetes \(ACK\) and are free of charge. The configuration of each worker node is 4 CPU cores and 8 GB of memory. Therefore, the total computing capacity of the cluster is 8 CPU cores and 16 GB of memory. An offline Job that requests 16 CPU cores and 32 GB of memory is deployed to process data. The Job failed to run in the managed Kubernetes cluster because the cluster can provide only 8 CPU cores and 16 GB of memory. The computing resources provided by the cluster do not meet the requirement of the Job. To resolve this issue, you can schedule the Job to a virtual node and run the Job without using the computing resources provided by the nodes in the cluster.

## Prerequisites

-   A managed Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A virtual node is deployed in the cluster. For more information, see [Deploy the virtual node controller and use it to create Elastic Container Instance-based pods](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).
-   The virtual-node-affinity-injection: enabled label is added to the vk namespace. For more information, see [Create a pod in a namespace with specified labels](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).
-   You are connected to the cluster by using kubectl. For more information, see [t16645.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Procedure

1.  Use the kubectl client to create a job.yaml file based on the following code:

    ```
    kind: Job
    metadata:
      name: pi
    spec:
      template:
        spec:
          containers:
          - name: pi
            image: perl
            command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
            resources:
              requests:
                cpu: 16
                memory: 32Gi
          restartPolicy: Never
      backoffLimit: 4
    ```

2.  Run the following command to deploy a Job:

    ```
    kubectl -n vk apply -f job.yaml
    ```

3.  Check the state of the pod.

    Run the following command to query the state of the pod:

    ```
    kubectl -n vk get pod -a
    ```

    Expected output:

    ```
    NAME       READY     STATUS      RESTARTS   AGE
    pi-7cmwv   0/1       Completed   0          2m
    ```

    Run the following command to query the state details of the pod:

    ```
    kubectl -n vk describe pod
    ```

    Expected output:

    ```
    Name:               pi-7cmwv
    Namespace:          vk
    Priority:           0
    PriorityClassName:  <none>
    Node:               virtual-kubelet/
    ...
    Events:
      Type    Reason                 Age   From               Message
      ----    ------                 ----  ----               -------
      Normal  Scheduled              3m    default-scheduler  Successfully assigned vk/pi-7cmwv to virtual-kubelet
      Normal  SuccessfulMountVolume  2m    kubelet, eci       MountVolume.SetUp succeeded for volume "default-token-b2tff"
      Normal  Pulling                2m    kubelet, eci       pulling image "perl"
      Normal  Pulled                 2m    kubelet, eci       Successfully pulled image "perl"
      Normal  Created                2m    kubelet, eci       Created container
      Normal  Started                2m    kubelet, eci       Started container
    ```

    **Note:** The pod on the virtual node is charged based on the amount of resources used by the pod. The system stops billing when the pod is completed.


Running a Job on a virtual node can reduce computing costs and O&M workloads. In addition, you do not need to worry about whether the cluster has sufficient computing resources or whether you need to scale in or out the number of nodes.


# Run a job by using a virtual node

This topic describes how to run a job by using a virtual node. This method minimizes cluster O&M costs because you do not need to create new nodes in your cluster to handle peak load.

Pods cannot run as expected when the nodes in a cluster have insufficient computing resources. However, creating a large number of nodes in a cluster may result in resource wastes. A virtual node offers near unlimited computing resources to a Kubernetes cluster and avoids resource wastes such as idle nodes. It allows you to create pods in a cluster based on your business requirements and ensures that the cluster can handle fluctuations in resource demands.

![Kubernetes clusters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9152005061/p49860.png)

For example, you create a managed Kubernetes cluster. The cluster contains two worker nodes and a managed master node that is free of change. The specification of each worker node is 4 cores and 8 GB of memory. Therefore, the total computing capacity of the cluster is 8 cores and 16 GB of memory. You want to run an offline job to process data. This job requires 16 cores and 32 GB of memory. You cannot run the job in the managed Kubernetes cluster because the cluster can provide only 8 cores and 16 GB of memory. The computing resources provided by the cluster do not meet the requirement of the job. To resolve this problem, you can schedule the job to a virtual node and run the job without using the computing resources of the nodes in the cluster.

## Prerequisites

-   A managed Kubernetes cluster is created. For more information, see [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md).
-   A virtual node is deployed in the cluster. For more information, see [Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).
-   The virtual-node-affinity-injection: enabled label is added to the namespace vk. For more information, see [create a pod by adding a label to a namespace](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md).

## Procedure

1.  [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

2.  Copy the following content to job.yaml, and run the `kubectl -n vk apply -f job.yaml` command to deploy a job in the cluster:

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

3.  To query the state of the pod,

    run the following command:

    ```
    kubectl -n vk get pod -a
    ```

    The following response is returned:

    ```
    NAME       READY     STATUS      RESTARTS   AGE
    pi-7cmwv   0/1       Completed   0          2m
    ```

    Run the following command:

    ```
    kubectl -n vk describe pod
    ```

    The following response is returned:

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

    **Note:** The pod on the virtual node is charged based on the amount of resources that you use. The system stops billing when the pod completes the job.


Running a job on a virtual node can reduce computing costs and O&M workloads. In addition, you do not need to worry about whether the cluster has sufficient computing resources or whether you need to scale in or out the number of nodes.


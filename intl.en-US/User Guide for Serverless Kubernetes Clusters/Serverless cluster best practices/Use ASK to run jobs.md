# Use ASK to run jobs

In a serverless Kubernetes \(ASK\) cluster, you can create pods as needed. The system stops billing after a pod is stopped and you do not need to reserve computing resources to handle jobs. This solves the issues of insufficient computing resources and saves you the need to expand the cluster. In addition, you can reduce the computing cost by using preemptible instances. This topic describes how to use ASK to create jobs to meet your business requirements.

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster management/Manage and access clusters/Use kubectl to connect to an ASK cluster.md)

## Procedure

1.  Use the kubectl client to create the job.yaml file and copy the following content into the file:

    ```
    apiVersion: batch/v1
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

2.  Run the following command to deploy a job:

    ```
    kubectl apply -f job.yaml
    ```

3.  Run the following commands to check the state of the pod.

    Run the following command to check the state of the pod:

    ```
    kubectl get pod
    ```

    ```
    NAME       READY   STATUS      RESTARTS   AGE
    pi-4f7w5   0/1     Completed   0          80s
    ```

    Run the following command to view detailed information about the state of the pod:

    ```
    kubectl describe pod
    ```

    ```
    Name:               pi-4f7w5
    Namespace:          default
    Priority:           0
    PriorityClassName:  <none>
    Node:               virtual-kubelet-cn-hongkong-b/10.10.66.169
    ...
    Events:
      Type    Reason                 Age   From          Message
      ----    ------                 ----  ----          -------
      Normal  SuccessfulMountVolume  114s  kubelet, eci  MountVolume.SetUp succeeded for volume "default-token-8k4jz"
      Normal  Pulling                113s  kubelet, eci  pulling image "perl"
      Normal  Pulled                 64s   kubelet, eci  Successfully pulled image "perl"
      Normal  Created                64s   kubelet, eci  Created container
      Normal  Started                64s   kubelet, eci  Started container
    ```

4.  To use a preemptible instance, add a preemptible instance annotation to the pod.

    For more information about how to a preemptible instance annotation, see [Use preemptible instances](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod management/Use preemptible instances.md).

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi
    spec:
      template:
        metadata:
          annotations:
            k8s.aliyun.com/eci-spot-strategy: SpotAsPriceGo
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



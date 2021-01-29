---
keyword: [resource scheduling, dynamically modify the upper limit of resources for a pod, resource-controller]
---

# Use resource-controller to dynamically modify the upper limit of resources for a pod

The resource-controller component is a controller that uses Kubernetes Custom Resource Definitions \(CRDs\) to control resource usage. It allows you to modify the upper limit of resources for a pod without restarting the pod. For example, you can modify the upper limit of CPU and memory resources. This enables containers in a pod to handle workloads as normal with the specified amount of resources. This topic describes how to dynamically modify the upper limit of resources for a pod after you deploy the resource-controller component.

-   Deploy the resource-controller component. For more information, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Upgrade cluster/Manage system components.md).
-   Use kubectl to connect to a Container Service for Kubernetes \(ACK\) cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

We recommend that you deploy resource-controller to dynamically modify the upper limit of resources for a pod in the following scenarios:

-   The pod is running. However, the specified CPU limit is low, which limits the speed of processes in the pod.
-   The CPU loads of the pod are high because you did not specify resource thresholds when you initialized the pod. You want to limit CPU usage of the pod without affecting other applications.
-   The memory usage of the pod is increasing and reaching the specified upper limit. You want to raise the memory upper limit without restarting the pod before the Out of Memory \(OOM\) killer is triggered.

In all preceding scenarios, the design principles of Kubernetes allow you to change the upper limit of resources for a pod only by modifying PodSpec. The pod is recreated after you change the upper limit. If an online application runs in the pod, users may fail to access the application and the network traffic may spike after the pod is recreated. If an offline task runs in the pod, all computing data generated within the previous hours may be lost after the pod is recreated.

## Dynamically modify the CPU and memory usage for a pod

1.  Deploy a task in a pod for simulation. The task is a stress testing program that uses 2 CPU cores and 256 MB of memory.

    Use the following template to deploy the simulation task and set the upper limit of CPU usage to 1:

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-demo
    spec:
      containers:
      - name: pod-demo
        image: polinux/stress
        resources:
          requests:
            memory: "50Mi"
          limits:
            memory: "1000Mi"
            cpu: 1
        command: ["stress"]
        args: ["--vm", "1", "--vm-bytes", "256M", "-c", "2", "--vm-hang", "1"]
    ```

    ![82](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8289091161/p205657.png)

    The preceding figure shows that the pod can use only one CPU core.

2.  Submit the following CRD template to dynamically modify the upper limit of CPU and memory usage:

    ```
    apiVersion: resources.alibabacloud.com/v1alpha1
    kind: Cgroups
    metadata:
      name: cgroups-sample
    spec:
      pods:
      - name: pod-demo
        containers:
        - name: pod-demo
          cpu: 2000m
          memory: 5000Mi
    ```

    ![83](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8289091161/p205658.png)

    The preceding figure shows that the CPU usage of the pod increases from one CPU core to two CPU cores.

3.  Run the following command to check the state of the pod:

    ```
    kubectl describe pod pod-demo
    ```

    The following is an example of the output, which indicates that the pod is running as expected and is not restarted:

    ```
    Events:
      Type    Reason            Age   From                                   Message
      ----    ------            ----  ----                                   -------
      Normal  Scheduled         13m   default-scheduler                      Successfully assigned default/pod-demo to cn-zhangjiakou.192.168.3.238
      Normal  Pulling           13m   kubelet, cn-zhangjiakou.192.168.3.238  Pulling image "polinux/stress"
      Normal  Pulled            13m   kubelet, cn-zhangjiakou.192.168.3.238  Successfully pulled image "polinux/stress"
      Normal  SuccessfulChange  60s   cgroups-controller                     Change pod pod-demo cpu to 2000
      Normal  SuccessfulChange  60s   cgroups-controller                     Change pod pod-demo memory to 524288000000
    ```


## Bind a pod to one or more CPUs

1.  Use the following template to run a stress testing program in a pod. The program uses 0.5 CPU cores.

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-demo
      annotations:
        cpuset-scheduler: 'true' #Add this annotation to enable topology-aware CPU scheduling.
    spec:
      containers:
      - name: pod-demo
        image: polinux/stress
        resources:
          requests:
            memory: "50Mi"
          limits:
            memory: "1000Mi"
            cpu: 0.5
        command: ["stress"]
        args: ["--vm", "1", "--vm-bytes", "556M", "-c", "2", "--vm-hang", "1"]
    ```

2.  View the CPU usage of the node `cn-beijing.192.168.8.241`. The following result indicates that the CPUs show different usage and the usage changes dynamically:

    ```
    top - 22:17:34 up 4 days, 10:29,  1 user,  load average: 0.33, 0.88, 0.95
    Tasks: 179 total,   3 running, 176 sleeping,   0 stopped,   0 zombie
    %Cpu0  : 13.1 us,  0.7 sy,  0.0 ni, 85.9 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu1  :  7.3 us,  7.7 sy,  0.0 ni, 84.7 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu2  : 12.4 us,  0.7 sy,  0.0 ni, 86.6 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu3  : 18.3 us,  0.7 sy,  0.0 ni, 80.7 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    ```

3.  Submit the following CRD template to bind the pod to CPU2 and CPU3:

    ```
    apiVersion: resources.alibabacloud.com/v1alpha1
    kind: Cgroups
    metadata:
      name: cgroups-sample-cpusetpod
    spec:
      pod:
        name: pod-demo
        namespace: default
        containers:
        - name: pod-demo
          cpuset-cpus: 2-3
    ```

4.  View the CPU usage of the node. The following result shows that the sum of the usage of CPU2 and CPU3 stays around 50% and the usage of each CPU stays around 25%. This indicates that the pod has been bound to CPU2 and CPU3 as expected and the pod is not restarted.

    ```
    top - 22:11:02 up 4 days, 10:22,  1 user,  load average: 0.04, 0.36, 0.84
    Tasks: 177 total,   3 running, 174 sleeping,   0 stopped,   0 zombie
    %Cpu0  :  2.7 us,  0.7 sy,  0.0 ni, 96.3 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu1  :  3.3 us,  1.0 sy,  0.0 ni, 95.3 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu2  : 27.2 us,  0.7 sy,  0.0 ni, 71.8 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu3  : 21.4 us,  5.7 sy,  0.0 ni, 72.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    ```


## Bind a Deployment to one or more CPUs

1.  The following template shows how to use a Deployment to run a stress testing program with two instances. Each instance uses 0.5 CPU cores.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: go-demo
      labels:
        app: go-demo
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: go-demo
      template:
        metadata:
          annotations:
            cpuset-scheduler: "true" #Add this annotation to enable topology-aware CPU scheduling.
          labels:
            app: go-demo
        spec:
          nodeName: cn-beijing.192.168.8.240 #Schedule to the same node.
          containers:
          - name: go-demo
            image: polinux/stress
            command: ["stress"]
            args: ["--vm", "1", "--vm-bytes", "556M", "-c", "1", "--vm-hang", "1"]
            imagePullPolicy: Always
            resources:
              requests:
                cpu: 0.5
              limits:
                cpu: 0.5  #Specify the value of resources.limit.cpu.
    ```

2.  View the CPU usage of the node `cn-beijing.192.168.8.240`. The following result indicates that the CPUs show different usage and the usage changes dynamically:

    ```
    top - 11:39:01 up 23:50,  2 users,  load average: 1.76, 1.91, 1.39
    Tasks: 189 total,   4 running, 185 sleeping,   0 stopped,   0 zombie
    %Cpu0  : 30.4 us,  5.4 sy,  0.0 ni, 64.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu1  : 29.4 us,  4.7 sy,  0.0 ni, 65.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu2  :  7.0 us,  8.7 sy,  0.0 ni, 84.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu3  : 50.3 us,  1.3 sy,  0.0 ni, 48.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    ```

3.  Submit the following CRD template to bind the Deployment to CPU2 and CPU3:

    ```
    apiVersion: resources.alibabacloud.com/v1alpha1
    kind: Cgroups
    metadata:
      name: cgroups-cpuset-sample
    spec:
      deployment:
        name: go-demo
        namespace: default
        containers:
        - name: go-demo
          cpuset-cpus: 2,3 #Bind the pod to CPU2 and CPU3.
    ```

4.  View the CPU usage of the node. The following result shows that the sum of the usage of CPU2 and CPU3 stays around 50%. This indicates that the two instances in the Deployment are separately bound to CPU2 and CPU3 as expected, and the pod is not restarted.

    ```
    top - 11:30:56 up 23:42,  2 users,  load average: 2.01, 1.95, 1.12
    Tasks: 180 total,   4 running, 176 sleeping,   0 stopped,   0 zombie
    %Cpu0  :  4.4 us,  2.4 sy,  0.0 ni, 93.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu1  :  4.4 us,  2.3 sy,  0.0 ni, 92.6 id,  0.3 wa,  0.0 hi,  0.3 si,  0.0 st
    %Cpu2  : 52.7 us,  8.0 sy,  0.0 ni, 39.0 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu3  : 50.7 us, 10.7 sy,  0.0 ni, 38.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    ```


## Control the IOPS of a disk

**Note:** To control the input/output operations per second \(IOPS\) of a disk, you must create worker nodes that use the Alibaba Cloud Linux 2 operating system.

1.  Deploy a Fio container. The container uses Fio to perform write stress tests on the disk.

    Use the following template to create a disk volume and mount the disk volume to the node `cn-beijing.192.168.0.182`. The device number of the disk is `/dev/vdb1` and the disk is mounted to the path /mnt.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: fio
      labels:
        app: fio
    spec:
      selector:
        matchLabels:
          app: fio
      template:
        metadata:
          labels:
            app: fio
        spec:
          nodeName: cn-beijing.192.168.0.182   # The pod is deployed on the node cn-beijing.192.168.0.182.
          containers:
          - name: fio
            image: registry.cn-beijing.aliyuncs.com/shuangkun/fio:v1
            command: ["sh", "-c"]
            # Use Fio to perform write stress tests on the disk.
            args: ["fio -filename=/data/test -direct=1 -iodepth 1 -thread -rw=write -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=12000 -group_reporting -name=mytest"]
            volumeMounts:
              - name: pvc
                mountPath: /data    #The disk volume is mounted to the path /data.
          volumes:
            - name: pvc
              hostPath:
                path: /mnt
    ```

2.  Limit the throughput of the pod by deploying the CRD that is used to control the IOPS of the disk.

    Use the following template to set the write `BPS`, read BPS, and total BPS of the `/dev/vdb1` disk to 1048576, 2097152, and 3145728:

    ```
    apiVersion: resources.alibabacloud.com/v1alpha1
    kind: Cgroups
    metadata:
      name: cgroups-sample-fio
    spec:
      pod:
        name: fio-6b6c469fdf-44h7v
        namespace: default
        containers:
        - name: fio
          blkio:
            device_write_bps: [{device: "/dev/vdb1", value: "1048576"}]
    ```

3.  View the monitoring data of the disk on the node `cn-beijing.192.168.0.182`, as shown in the following figure.

    ![84](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8289091161/p205663.png)

    ![85](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8289091161/p205669.png)

    The preceding figure shows that the throughput `BPS` of the pod is as expected and the pod is not restarted during modification.


## Topology-aware CPU scheduling

You can use resource-controller with ACK schedulers to facilitate CPU binding and automate CPU selection on multi-core physical machines, such as Intel, AMD, and ARM. For more information, see [Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Resource scheduling/Topology-aware CPU scheduling.md).

Topology-aware CPU scheduling manages CPU cores and hyper-threads in an appropriate manner to avoid the following issues: switchover between L1 cache and L2 cache, off-chip transmission across Non-Uniform Memory Access \(NUMA\), and frequent refreshing of L3 cache. This maximizes the CPU usage efficiency for CPU-intensive applications that run with multiple threads. For more information about topology-aware CPU scheduling, see the speech [Practice of Fine-grained Cgroups Resources Scheduling in Kubernetes](https://kccncna20.sched.com/event/f3cp) that is given by the ACK team at KubeCon 2020.

**Related topics**  


[Topology-aware CPU scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Introduction/Resource scheduling/Topology-aware CPU scheduling.md)


---
keyword: [资源调度, 动态修改Pod资源上限, resource-controller]
---

# 通过resource-controller组件动态修改Pod资源上限

resource-controller是一款基于Kubernetes CRD（CustomResourceDefinition）的控制器，它能够在不重启Pod的情况下，动态地修改Pod的CPU、内存等资源，从而保证容器能够在不影响当前业务的情况下按照您期望的资源使用率去运行。本文主要介绍部署resource-controller组件后如何动态地修改Pod资源上限。

-   部署resource-controller组件。具体操作，请参见[管理组件](/intl.zh-CN/Kubernetes集群用户指南/集群/升级集群/管理组件.md)。
-   通过kubectl连接Kubernetes集群。具体操作，请参见[通过kubectl连接Kubernetes集群](/intl.zh-CN/Kubernetes集群用户指南/集群/连接集群/通过kubectl连接Kubernetes集群.md)。

当您在使用Kubernetes的过程中遇到以下情况时，建议部署组件resource-controller动态地修改Pod资源上限：

-   Pod已经在运行中了，突然发现CPU Limit设置的值偏小，限制了进程的运行速度。
-   Pod初始化时没有设置Limit，后来发现CPU负载过高，希望限制CPU Limit，但又不要影响其他应用。
-   Pod内存使用率逐渐升高，快要接近Memory Limit，且马上就会触发OOM（Out Of Memory）Killer，希望在不重启Pod的前提下提高内存的Limit。

不论您处于以上情况的哪一种情况，根据目前Kubernetes的设计规范，只能重新编辑PodSpec，设计完资源的Limit后触发Pod重建。如果您使用的Pod是一个在线应用，可能会导致应用再次访问失败，且流量异常升高；如果您使用的Pod是一个离线任务，可能会导致前面几个小时计算数据全部丢失。

## 动态调整Pod的CPU和内存

1.  部署一个模拟的Pod任务，它是一个压力测试程序，会尝试使用2个CPU和256 MB的内存资源。

    使用以下模板样例部署一个模拟的Pod任务，将CPU的限制值设置为1。

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

    ![82](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3649788061/p205657.png)

    由上图可以看出，目前Pod被限制住了，只能使用1个CPU。

2.  提交以下模板所示的CRD，动态地修改CPU和内存的限制值。

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

    ![83](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/3649788061/p205658.png)

    由上图可以看出，Pod的CPU使用率由原来的1个CPU飙升到了2个CPU。

3.  执行以下命令，查看Pod运行情况。

    ```
    kubectl describe pod pod-demo
    ```

    系统输出类似以下结果，说明Pod运行正常，没有被重启。

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


## 绑定Pod到CPU

1.  使用以下模板样例，让Pod运行一个压测程序，使用的资源为0.5个CPU。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-demo
      annotations:
        cpuset-scheduler: 'true' #添加注解，表示CPU拓扑感知调度。
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

2.  查看节点`cn-beijing.192.168.8.241`上各CPU利用率，可以发现各个CPU的利用率各不相同，且一直在动态变化。

    ```
    top - 22:17:34 up 4 days, 10:29,  1 user,  load average: 0.33, 0.88, 0.95
    Tasks: 179 total,   3 running, 176 sleeping,   0 stopped,   0 zombie
    %Cpu0  : 13.1 us,  0.7 sy,  0.0 ni, 85.9 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu1  :  7.3 us,  7.7 sy,  0.0 ni, 84.7 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu2  : 12.4 us,  0.7 sy,  0.0 ni, 86.6 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu3  : 18.3 us,  0.7 sy,  0.0 ni, 80.7 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    ```

3.  使用以下模板样例，提交绑定CPU的CRD，将Pod绑定到CPU2和CPU3上。

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

4.  查看该节点各CPU利用率，发现CPU2、CPU3的使用率加起来一直稳定在了50%左右，每个CPU的利用率稳定在25%左右，说明该Pod被绑定在了CPU2和CPU3上，与预期保持一致，且Pod没有被重启。

    ```
    top - 22:11:02 up 4 days, 10:22,  1 user,  load average: 0.04, 0.36, 0.84
    Tasks: 177 total,   3 running, 174 sleeping,   0 stopped,   0 zombie
    %Cpu0  :  2.7 us,  0.7 sy,  0.0 ni, 96.3 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu1  :  3.3 us,  1.0 sy,  0.0 ni, 95.3 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu2  : 27.2 us,  0.7 sy,  0.0 ni, 71.8 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu3  : 21.4 us,  5.7 sy,  0.0 ni, 72.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    ```


## 绑定Deployment到CPU

1.  使用以下模板样例，让Deployment运行一个共有两个实例的压测程序，每个实例使用的资源为0.5个CPU。

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
            cpuset-scheduler: "true" #添加注解，表示CPU拓扑感知调度。
          labels:
            app: go-demo
        spec:
          nodeName: cn-beijing.192.168.8.240 #选择调度到同一个节点上。
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
                cpu: 0.5  #需要设置resources.limit.cpu值。
    ```

2.  查看节点`cn-beijing.192.168.8.240`上各CPU利用率，可以发现各个CPU的利用率各不相同，且一直在动态变化。

    ```
    top - 11:39:01 up 23:50,  2 users,  load average: 1.76, 1.91, 1.39
    Tasks: 189 total,   4 running, 185 sleeping,   0 stopped,   0 zombie
    %Cpu0  : 30.4 us,  5.4 sy,  0.0 ni, 64.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu1  : 29.4 us,  4.7 sy,  0.0 ni, 65.9 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu2  :  7.0 us,  8.7 sy,  0.0 ni, 84.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu3  : 50.3 us,  1.3 sy,  0.0 ni, 48.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    ```

3.  使用以下模板样例，提交绑定CPU的CRD，将该Deployment中的Pod绑定在CPU2和CPU3上。

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
          cpuset-cpus: 2,3 #绑定Pod到CPU2、CPU3上。
    ```

4.  查看该节点各CPU利用率，发现CPU2、CPU3的使用率一直稳定在了50%左右，说明该Deployment的两个实例分别绑定在了CPU2、CPU3上，与预期保持一致，且Pod没有被重启。

    ```
    top - 11:30:56 up 23:42,  2 users,  load average: 2.01, 1.95, 1.12
    Tasks: 180 total,   4 running, 176 sleeping,   0 stopped,   0 zombie
    %Cpu0  :  4.4 us,  2.4 sy,  0.0 ni, 93.2 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu1  :  4.4 us,  2.3 sy,  0.0 ni, 92.6 id,  0.3 wa,  0.0 hi,  0.3 si,  0.0 st
    %Cpu2  : 52.7 us,  8.0 sy,  0.0 ni, 39.0 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
    %Cpu3  : 50.7 us, 10.7 sy,  0.0 ni, 38.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
    ```


## 控制磁盘IOPS

**说明：** 如果需要控制磁盘IOPS，请您使用Alibaba Cloud Linux 2操作系统创建Worker节点。

1.  部署用于测试的Fio容器，该容器使用Fio工具对磁盘进行写的压测。

    使用以下模板样例，创建一块挂载在`cn-beijing.192.168.0.182`节点上云盘，挂载的设备号为`/dev/vdb1`，挂载路径为/mnt。

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
          nodeName: cn-beijing.192.168.0.182   # Pod部署在节点cn-beijing.192.168.0.182上。
          containers:
          - name: fio
            image: registry.cn-beijing.aliyuncs.com/shuangkun/fio:v1
            command: ["sh", "-c"]
            # 使用Fio工具对磁盘IOPS进行写测试。
            args: ["fio -filename=/data/test -direct=1 -iodepth 1 -thread -rw=write -ioengine=psync -bs=16k -size=2G -numjobs=10 -runtime=12000 -group_reporting -name=mytest"]
            volumeMounts:
              - name: pvc
                mountPath: /data    #挂载在/data的路径下。
          volumes:
            - name: pvc
              hostPath:
                path: /mnt
    ```

2.  部署控制磁盘IOPS的CRD，将Pod的吞吐进行限制。

    使用以下模板样例，分别把设备`/dev/vdb1`的`BPS`限制为：1048576、2097152、3145728。

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

3.  查看节点`cn-beijing.192.168.0.182`上的磁盘监控，如下图所示。

    ![84](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4649788061/p205663.png)

    ![85](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4649788061/p205669.png)

    如上图所示，说明Pod的吞吐`BPS`和预期保持了一致，且修改过程中Pod没有被重启。


## CPU拓扑感知调度

resource-controller结合ACK的调度器可以在多核物理机（Intel、AMD和ARM）上实现更优的CPU绑定和自动选择功能。具体操作，请参见[CPU拓扑感知调度](/intl.zh-CN/Kubernetes集群用户指南/调度/CPU和内存调度/CPU拓扑感知调度.md)。

CPU拓扑感知调度实现对物理核心和超线程的合理使用，避免L1或L2换入换出，以及跨NUMA的片外传输和L3的频繁刷新，实现对多线程CPU密集应用的最大化CPU使用效率。关于更多CPU拓扑感知调度能力，请参见ACK团队在KubeCon 2020的演讲[《Practice of Fine-grained Cgroups Resources Scheduling in Kubernetes》](https://kccncna20.sched.com/event/f3cp)。

**相关文档**  


[CPU拓扑感知调度](/intl.zh-CN/Kubernetes集群用户指南/调度/CPU和内存调度/CPU拓扑感知调度.md)


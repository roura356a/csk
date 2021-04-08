---
keyword: GPU监控常见问题
---

# 排查GPU监控常见问题

当GPU监控大盘异常或无数据时，您可以按照本文描述的操作步骤排查GPU监控常见问题。

## 操作步骤

**步骤一：查看集群中是否有GPU节点**

1.  登录[容器服务管理控制台](https://cs.console.aliyun.com)。

2.  在控制台左侧导航栏中，单击**集群**。

3.  在集群列表页面中，单击目标集群名称或者目标集群右侧**操作**列下的**详情**。

4.  在集群管理页左侧导航栏中，选择**节点管理** \> **节点**。

5.  在**节点**页面，查看目标集群中是否有GPU节点。

    **说明：** 在**节点**页面的**配置**列，如果配置名称包含**\*\*\*\*ecs.gn\*\*\*\***，则说明该集群中有GPU节点。


**步骤二：查看ack-arms-prometheus是否正确安装**

1.  查看目标集群是否安装ack-arms-prometheus。具体操作，请参见[开启阿里云Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/可观测性/监控管理/阿里云Prometheus监控.md)。

2.  如果已安装ack-arms-prometheus，执行以下命令查看ack-arms-prometheus的Pod状态。

    ```
    kubectl get pods -n arms-prom
    ```

    预期输出：

    ```
    NAME                                             READY   STATUS    RESTARTS   AGE
    arms-prom-ack-arms-prometheus-866cfd9f8f-x8jxl   1/1     Running   0          26d
    ```

    如果是Running状态，说明Pod运行正常。如果Pod的状态不是Running，则执行`kubectl describe pod`命令，查看Pod状态不正常原因。


**步骤三：检查ack-prometheus-gpu-exporter是否成功部署**

执行以下命令，查看Pod的运行状态和数量。

```
kubectl get pods -n arms-prom
```

预期输出：

```
NAME                                                  READY   STATUS    RESTARTS   AGE
ack-prometheus-gpu-exporter-6kpj7                     1/1     Running   0          7d19h
ack-prometheus-gpu-exporter-bkbf8                     1/1     Running   0          18h
ack-prometheus-gpu-exporter-blbnq                     1/1     Running   0          18h
```

从上述输出信息，可以知道Pod数量和GPU节点数一致，且Pod的状态是Running，说明ack-prometheus-gpu-exporter已在相应GPU节点成功部署。如果Pod的状态不是Running，则执行`kubectl describe pod`命令，查看Pod状态不正常原因。

**步骤四：检查ack-prometheus-gpu-exporter是否成功采集到数据**

1.  执行以下命令，SSH登录到目标集群节点。

    ```
    ssh root@127.0.XX.XX
    ```

    -   root：用户自定义用户名。
    -   127.0.XX.XX：目标集群的公网IP访问地址。
2.  执行以下命令，查看Pod的内网IP。

    ```
    kubectl get pods -n arms-prom -o wide
    ```

    预期输出：

    ```
    NAME                                                   READY   STATUS    RESTARTS   AGE     IP             NODE                      NOMINATED NODE   READINESS GATES
    ack-prometheus-gpu-exporter-4rdtl                      1/1     Running   0          7h6m    172.21.XX.XX   cn-beijing.192.168.0.22   <none>           <none>
    ack-prometheus-gpu-exporter-vdkqf                      1/1     Running   0          6d16h   172.21.XX.XX   cn-beijing.192.168.94.7   <none>           <none>
    ack-prometheus-gpu-exporter-x7v48                      1/1     Running   0          7h6m    172.21.XX.XX   cn-beijing.192.168.0.23   <none>           <none>
    ```

3.  执行以下命令，调用`gpu exporter`服务，获取GPU指标信息。

    **说明：** ack-prometheus-gpu-exporter的默认端口是9445。

    ```
    curl 172.21.XX.XX:9445 | grep "nvidia_gpu"
    ```

    预期输出：

    ```
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100  7518  100  7518    0     0   101k      0 --:--:-- --:--:-- --:--:--  101k
    # HELP nvidia_gpu_duty_cycle Percent of time over the past sample period during which one or more kernels were executing on the GPU device
    # TYPE nvidia_gpu_duty_cycle gauge
    nvidia_gpu_duty_cycle{allocate_mode="exclusive",container_name="tfserving-gpu",minor_number="0",name="Tesla T4",namespace_name="default",node_name="cn-beijing.192.168.0.22",pod_name="fashion-mnist-eci-2-predictor-0-tfserving-proxy-tfserving-v789b",uuid="GPU-293f6608-281a-cc66-fcb3-0d366f32a31d"} 0
    # HELP nvidia_gpu_memory_total_bytes Total memory of the GPU device
    # TYPE nvidia_gpu_memory_total_bytes gauge
    nvidia_gpu_memory_total_bytes{allocate_mode="exclusive",container_name="tfserving-gpu",minor_number="0",name="Tesla T4",namespace_name="default",node_name="cn-beijing.192.168.0.22",pod_name="fashion-mnist-eci-2-predictor-0-tfserving-proxy-tfserving-v789b",uuid="GPU-293f6608-281a-cc66-fcb3-0d366f32a31d"} 1.5811477504e+10
    # HELP nvidia_gpu_memory_used_bytes Memory used by the GPU device
    # TYPE nvidia_gpu_memory_used_bytes gauge
    nvidia_gpu_memory_used_bytes{allocate_mode="exclusive",container_name="tfserving-gpu",minor_number="0",name="Tesla T4",namespace_name="default",node_name="cn-beijing.192.168.0.22",pod_name="fashion-mnist-eci-2-predictor-0-tfserving-proxy-tfserving-v789b",uuid="GPU-293f6608-281a-cc66-fcb3-0d366f32a31d"} 1.488453632e+10
    # HELP nvidia_gpu_num_devices Number of GPU devices
    # TYPE nvidia_gpu_num_devices gauge
    nvidia_gpu_num_devices{node_name="cn-beijing.192.168.0.22"} 1
    # HELP nvidia_gpu_power_usage_milliwatts Power usage of the GPU device in watts
    # TYPE nvidia_gpu_power_usage_milliwatts gauge
    nvidia_gpu_power_usage_milliwatts{allocate_mode="exclusive",container_name="tfserving-gpu",minor_number="0",name="Tesla T4",namespace_name="default",node_name="cn-beijing.192.168.0.22",pod_name="fashion-mnist-eci-2-predictor-0-tfserving-proxy-tfserving-v789b",uuid="GPU-293f6608-281a-cc66-fcb3-0d366f32a31d"} 27000
    # HELP nvidia_gpu_temperature_celsius Temperature of the GPU device in celsius
    # TYPE nvidia_gpu_temperature_celsius gauge
    nvidia_gpu_temperature_celsius{allocate_mode="exclusive",container_name="tfserving-gpu",minor_number="0",name="Tesla T4",namespace_name="default",node_name="cn-beijing.192.168.0.22",pod_name="fashion-mnist-eci-2-predictor-0-tfserving-proxy-tfserving-v789b",uuid="GPU-293f6608-281a-cc66-fcb3-0d366f32a31d"} 44
    ```

    如果输出的数据中有`nvidia_gpu`开头的指标信息，说明ack-prometheus-gpu-exporter可以成功采集数据。


**相关文档**  


[配置GPU Prometheus监控](/cn.zh-CN/Kubernetes集群用户指南/GPU/NPU/运维管理/配置GPU Prometheus监控.md)


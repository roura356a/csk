---
keyword: GPU monitoring issues
---

# Troubleshoot issues in GPU monitoring

When exceptions occur or no records are found on GPU monitoring dashboards, you can perform the steps in this topic to troubleshoot the issues.

## Procedure

**Step 1: Check whether GPU nodes exist in the cluster**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node**.

5.  On the **Nodes** page, check whether GPU nodes exist in the cluster.

    **Note:** On the **Nodes** page, if the values in the **Configuration** column contains **\*\*\*\*ecs.gn\*\*\*\***, the cluster has GPU nodes.


**Step 2: Check whether ack-arms-prometheus is installed**

1.  Check whether ack-arms-prometheus is installed in the cluster. For more information, see [Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/ARMS Prometheus.md).

2.  If ack-arms-prometheus is installed, run the following command to query the pods of ack-arms-prometheus:

    ```
    kubectl get pods -n arms-prom
    ```

    Expected output:

    ```
    NAME                                             READY   STATUS    RESTARTS   AGE
    arms-prom-ack-arms-prometheus-866cfd9f8f-x8jxl   1/1     Running   0          26d
    ```

    If the pods are in the Running state, the pods run as expected. If the pods are not in the Running state, run the `kubectl describe pod` command to query the reason why the pods are not running.


**Step 3: Check whether ack-prometheus-gpu-exporter is deployed**

Run the following command to query the status and quantity of pods:

```
kubectl get pods -n arms-prom
```

Expected output:

```
NAME                                                  READY   STATUS    RESTARTS   AGE
ack-prometheus-gpu-exporter-6kpj7                     1/1     Running   0          7d19h
ack-prometheus-gpu-exporter-bkbf8                     1/1     Running   0          18h
ack-prometheus-gpu-exporter-blbnq                     1/1     Running   0          18h
```

The preceding output indicates that the number of pods is the same as the number of GPU nodes and the pods are in the Running state. This means that ack-prometheus-gpu-exporter is deployed on the GPU nodes. If the pods are not in the Running state, run the `kubectl describe pod` command to query the reason why the pods are not running.

**Step 4: Check whether data is collected by ack-prometheus-gpu-exporter**

1.  Run the following command to log on to a node in the cluster by using SSH:

    ```
    ssh root@127.0.XX.XX
    ```

    -   root: the custom account name.
    -   127.0.XX.XX: the public IP address of the node.
2.  Run the following command to query the internal IP addresses of the pods:

    ```
    kubectl get pods -n arms-prom -o wide
    ```

    Expected output:

    ```
    NAME                                                   READY   STATUS    RESTARTS   AGE     IP             NODE                      NOMINATED NODE   READINESS GATES
    ack-prometheus-gpu-exporter-4rdtl                      1/1     Running   0          7h6m    172.21.XX.XX   cn-beijing.192.168.0.22   <none>           <none>
    ack-prometheus-gpu-exporter-vdkqf                      1/1     Running   0          6d16h   172.21.XX.XX   cn-beijing.192.168.94.7   <none>           <none>
    ack-prometheus-gpu-exporter-x7v48                      1/1     Running   0          7h6m    172.21.XX.XX   cn-beijing.192.168.0.23   <none>           <none>
    ```

3.  Run the following command to call the `gpu exporter` service to obtain GPU metrics:

    **Note:** By default, ack-prometheus-gpu-exporter uses port 9445.

    ```
    curl 172.21.XX.XX:9445 | grep "nvidia_gpu"
    ```

    Expected output:

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

    If the output contains metric records that start with `nvidia_gpu`, data is collected by ack-prometheus-gpu-exporter.


**Related topics**  


[t1963645.md\#]()


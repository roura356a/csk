---
keyword: [ARMS Prometheus, GPU, resource monitoring]
---

# Use ARMS Prometheus to monitor the GPU resources of a Kubernetes cluster

This topic describes how to use Application Real-Time Monitoring Service \(ARMS\) Prometheus to monitor the GPU resources of a Kubernetes cluster.

Make sure that the following operations are completed:

-   [Create a managed Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a managed Kubernetes cluster with GPU-accelerated nodes.md) or [Create a dedicated Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a dedicated Kubernetes cluster with GPU-accelerated nodes.md).
-   [ARMS Prometheus is enabled](https://arms-intl.console.aliyun.com/).

## Install ARMS Prometheus

Before you use ARMS Prometheus to monitor GPU resources, you must install ARMS Prometheus in your cluster.

You can install ARMS Prometheus in three ways. For more information, see [Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md).

## Use ARMS Prometheus to monitor GPU resources

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Operations** \> **Prometheus Monitoring**.

4.  On the **Prometheus Monitoring** page, you can click the **GPU APP** tab to view the **GPU APP** dashboard and click the **GPU Node** tab to view the **GPU Node** dashboard.

    -   The **GPU APP** dashboard displays monitoring information about the GPU resources used by each pod.
    -   The **GPU Node** dashboard displays monitoring information about the GPU resource usage of each node.
5.  Use the following YAML template to deploy an application on a GPU-accelerated node and test the monitoring of GPU resources.

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: bert-intent-detection
    spec:
      replicas: 1
      template:
        metadata:
          labels:
            app: bert-intent-detection
        spec:
          containers:
          - name: bert-container
            image: registry.cn-beijing.aliyuncs.com/ai-samples/bert-intent-detection:1.0.1
            ports:
            - containerPort: 80
            resources:
              limits:
                nvidia.com/gpu: 1
    ---
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: bert-intent-detection
      name: bert-intent-detection-svc
    spec:
      ports:
      - port: 8500
        targetPort: 80
      selector:
        app: bert-intent-detection
      type: LoadBalancer
    ```

6.  On the **Prometheus Monitoring** page, click the **GPU APP** tab.

    On the **GPU APP** tab, you can view various metrics of the GPU resources used by each pod, including the used GPU memory, GPU memory usage, power consumption, and stability. You can also view the applications deployed on each GPU-accelerated node.

    ![GPU](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2298476261/p175169.jpeg)

7.  Perform stress tests on the application deployed on the GPU-accelerated node and check the changes of metrics.

    1.  Run the following command to query the Service of an inference task and the IP address of the Service:

        ```
        kubectl get svc bert-intent-detection-svc
        ```

        Expected output:

        ```
        NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
        bert-intent-detection-svc   LoadBalancer   172.23.5.253   123.56.xx.xx   8500:32451/TCP   14m
        ```

    2.  Run the following command to perform stress tests:

        ```
        hey -z 10m -c 100 "http://123.56.xx.xx:8500/predict?query=music"
        ```

        The metrics indicate an obvious increase of the GPU memory usage, as shown in the following figure.

        ![GPU2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3833386261/p175170.jpeg)



---
keyword: [auto scaling, configure Horizontal Pod Autoscaler \(HPA\), metrics]
---

# Implement horizontal auto scaling based on Alibaba Cloud metrics

This topic describes how to implement auto scaling based on Alibaba Cloud metrics.

[A Container Service for Kubernetes \(ACK\) cluster is created.](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md)

In many scenarios, you may want to scale pods based on metrics such as the HTTP request rate and the queries per second \(QPS\) of the Ingress. By default, HPA does not support custom or external metrics. However, Kubernetes enables the external metrics API that allows you to implement auto scaling in a flexible manner.

## Deploy alibaba-cloud-metrics-adapter

1.  In the left-side navigation pane of the [ACK console](https://cs.console.aliyun.com/), choose **Marketplace** \> **App Catalog**. On the App Catalog page, choose **Alibaba Cloud Apps** \> **Auto Scaling**.

2.  Click the **ack-alibaba-cloud-metrics-adapter** icon. On the page that appears, configure the parameters in the **Deploy** section, and click **Create**.

    In the left-side navigation pane of the ACK console, click **Clusters**. On the Clusters page, find the cluster in which you deploy the component and click **Details** in the **Actions** column. On the details page of the cluster, click **Releases**. On the **Helm** page, you can view that **ack-alibaba-cloud-metrics-adapter** is deployed in the cluster.


## Examples

The following example shows how to configure HPA by creating a Deployment and a Service named nginx.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the cluster name or click **Applications** in the **Actions** column.
4.  On the Deployments page, click **Create from Template**.
5.  On the Create from Template page, use the following template to create a Deployment and a ClusterIP type Service. Then, click **Create**.

    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
      name: nginx-deployment-basic
      labels:
        app: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.7.9 
            ports:
            - containerPort: 80
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      namespace: default
    spec:
      ports:
        - port: 80
          protocol: TCP
          targetPort: 80
      selector:
        app: nginx
      type: ClusterIP
    ```

6.  In the left-side navigation pane, click **Ingresses**. On the Ingresses page, click **Create** in the upper-right corner.
7.  In the Create dialog box, set the parameters and click **Create**. After you create an Ingress, the **Ingresses** page appears.
8.  On the Ingresses page, find the newly created Ingress and click **Details** in the Actions column to view information about the Ingress.
9.  Configure HPA.
    1.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**.
    2.  On the Templates page, select **hpa** and click **Create Application** to deploy an application with the following YAML file:

        ```
        apiVersion: autoscaling/v2beta2
        kind: HorizontalPodAutoscaler
        metadata:
          name: ingress-hpa
        spec:
          scaleTargetRef:
            apiVersion: apps/v1
            kind: Deployment
            name: nginx-deployment-basic
          minReplicas: 2
          maxReplicas: 10
          metrics:
            - type: External
              external:
                metric:
                  name: sls_ingress_qps
                  selector:
                    matchLabels:
                      sls.project: "***"
                      sls.logstore: "nginx-ingress"
                      sls.ingress.route: "default-nginx-80"
                target:
                  type: AverageValue
                  averageValue: 10
            - type: External
              external:
                metric:
                  name: sls_ingress_latency_p9999
                  selector:
                    matchLabels:
                      # default ingress log project is k8s-log-clusterId
                      sls.project: "***"
                      # default ingress logstre is nginx-ingress
                      sls.logstore: "nginx-ingress"
                      # namespace-svc-port
                      sls.ingress.route: "default-nginx-80"
                      # sls vpc endpoint, default true
                      # sls.internal.endpoint:ture
                target:
                  type: Value
                  # sls_ingress_latency_p9999 > 10ms
                  value: 10
        ```

        The following table describes the parameters that are used to configure HPA.

        |Parameter|Description|
        |---------|-----------|
        |sls.ingress.route|Set the value in the `<namespace>-<svc>-<port>` format, for example, default-nginx-80. This parameter is required.

**Note:** `<namespace>` specifies the namespace to which the Ingress belongs. `<svc>` specifies the name of the Service that you selected when you created the Ingress. `<port>` specifies the port of the Service. |
        |sls.logstore|The name of the Logstore in Log Service. This parameter is required.|
        |sls.project|The name of the Log Service project. This parameter is required.|
        |sls.internal.endpoint|Specifies whether to access Log Service over the internal network or the Internet. Default value: true. If you set the value to true, you can access Log Service over the internal network. If you set the value to false, you can access Log Service over the Internet.|

        **Note:**

        The sls\_ingress\_qps and sls\_ingress\_latency\_p9999 metrics are used by HPA to automatically scale pods. In the target sections, each metric has a different type value:

        -   The type value of the sls\_ingress\_qps metric is set to AverageValue. This indicates that the metric value is the result of dividing the total QPS by the number of pods.
        -   The type value of the sls\_ingress\_latency\_p9999 metric is set to Value. This indicates that the latency is not divided by the number of pods.
        The two type values are commonly used in HPA configurations.

    3.  In the upper-right corner of the Templates - HPA page, click **Create**.
10. After HPA is configured, execute the following script to run a stress test:

    ```
    #!/bin/bash
    ##Use Apache Benchmark to send requests to the Service exposed by the Ingress. The test lasts 300 seconds and 10 concurrent requests are sent per second.
    ab -t 300 -c 10 <The domain name of the Ingress>
    ```

11. Check the scaling status.
    1.  In the left-side navigation pane of the ACK console, click **Clusters**. On the Clusters page, find the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column.
    2.  Run the `kubectl get hpa ingress-hpa` command to check the scaling status.

        If the value of REPLICAS is the same as the value of MAXPODS, the pods are scaled out.

        ![hpa](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9254579161/p98107.png)


## FAQ

-   What do I do if the TARGETS column shows <unknow\> after I run the `kubectl get hpa` command?

    Perform the following operations to troubleshoot the issue:

    1.  Run the `kubectl describe hpa <hpa_name>` command to check why HPA does not function as normal.
        -   If the value of AbleToScale is False in the Conditions field, check whether the Deployment is created as normal.
        -   If the value of ScalingActive is False in the Conditions field, proceed to the next step.
    2.  Run the `kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/"` command. If Error from server \(NotFound\): the server could not find the requested resource is returned, verify the status of alibaba-cloud-metrics-adapter.

        If the status of alibaba-cloud-metrics-adapter is normal, check whether the HPA metrics are related to the Ingress. If the metrics are related to the Ingress, make sure that you deploy the Log Service component before ack-alibaba-cloud-metrics-adapter is deployed. For more information, see [Monitor and analyze the logs of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Monitor and analyze the logs of nginx-ingress.md).

    3.  Make sure that the values of the HPA metrics are valid. The value of sls.ingress.route must be in the `<namespace>-<svc>-<port>` format.
        -   `<namespace>` specifies the namespace to which the Ingress belongs.
        -   `<svc>` specifies the name of the Service that you selected when you created the Ingress.
        -   `<port>` specifies the port of the Service.
-   Where can I find the metrics that are supported by HPA?

    For more information about the metrics that are supported by HPA, see [Alibaba Cloud metrics adapter](https://github.com/AliyunContainerService/alibaba-cloud-metrics-adapter). The following table describes the commonly used metrics.

    |Metric|Description|Additional parameter|
    |------|-----------|--------------------|
    |sls\_ingress\_qps|The number of requests that the Ingress can process per second based on a specific routing rule.|sls.ingress.route|
    |sls\_ingress\_latency\_avg|The average latency of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p50|The maximum latency for the fastest 50% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p95|The maximum latency for the fastest 95% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p99|The maximum latency for the fastest 99% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p9999|The maximum latency for the fastest 99.99% of all requests.|sls.ingress.route|
    |sls\_ingress\_inflow|The inbound bandwidth of the Ingress.|sls.ingress.route|



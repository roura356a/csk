---
keyword: [auto scaling, configure the Horizontal Pod Autoscaler \(HPA\), metrics]
---

# Implement horizontal auto scaling based on Alibaba Cloud metrics

This topic describes how to implement auto scaling based on Alibaba Cloud metrics.

A Kubernetes cluster is created. For more information, see [Create a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).

In many scenarios, you may want to scale applications based on metrics such as the HTTP request rate and the query rate of the ingress. By default, the HPA does not support custom or external metrics. However, Kubernetes enables the external metrics API that allows you to implement auto scaling in a flexible manner.

## Deploy alibaba-cloud-metrics-adapter

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com/). In the left-side navigation pane, choose **Marketplace** \> **App Catalog**. On the App Catalog page, choose **Alibaba Cloud Apps** \> **Auto Scaling**.

2.  Click the **ack-alibaba-cloud-metrics-adapter** icon. In the **Deploy** section on the right side, click **Create**.

    In the left-side navigation pane, click **Clusters**. On the Clusters page, find the cluster that you want to view and click **Details** in the **Actions** column. On the details page of the cluster, click **Releases**. On the **Helm** tab, you can view that **ack-alibaba-cloud-metrics-adapter** is deployed on the cluster.


## Examples

The following example describes how to configure the HPA by creating a deployment and a service named nginx.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane, click **Clusters**.
3.  On the Clusters page, find the cluster that you want to manage and click the cluster name or click **Applications** in the **Actions** column.
4.  On the Deployments tab, click **Create from Template**.
5.  On the page that appears, enter the following code to create a deployment and a ClusterIP service. Then, click **Create**.

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

6.  In the left-side navigation pane, click **Ingresses**. On the Ingress page, click **Create** in the upper-right corner.
7.  In the Create dialog box, set the parameters and click **Create**. After you create an ingress, the **Ingress** page appears.
8.  On the Ingress page, find the newly created ingress and click **Details** in the Actions column to view ingress information.
9.  Configure the HPA.
    1.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**.
    2.  On the Templates page, select **HPA** and click **Create Application** to deploy the following YAML file.

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

        The following table lists the HPA parameters that are used in the preceding example.

        |Parameter|Description|
        |---------|-----------|
        |sls.ingress.route|-|
        |sls.logstore|-|
        |sls.project|-|
        |sls.internal.endpoint|Specifies whether to access Log Service over an internal network or the Internet. Default value: true. If you set the value to true, you can access Log Service over an internal network. If you set the value to false, you can access Log Service over the Internet.|

        **Note:**

        In the preceding example, the HPA implements auto scaling based on metrics sls\_ingress\_qps and sls\_ingress\_latency\_p9999. In the target section, each metric has a different type value.

        -   The type of the metric sls\_ingress\_qps is set to AverageValue. This indicates that the query rate of the ingress is an average value by dividing the number of pods.
        -   The type of the metric sls\_ingress\_latency\_p9999 is set to Value. This indicates the maximum latency for the fastest 99.99% of all requests.
        The preceding values of type are commonly used in the HPA configurations.

    3.  On the Templates - HPA page, click **Create**.
10. After the HPA is configured, run the following script to test the configurations:

    ```
    #! /bin/bash
    ##Use Apache Bench to send requests to the service that can be accessed through an ingress. The test lasts 300 seconds and 10 concurrent requests are sent per second.
    ab -t 300 -c 10 <The domain of the ingress>
    ```

11. Check the scaling status.
    1.  In the left-side navigation pane of the ACK console, click **Clusters**. On the Clusters page, find the cluster that you want to manage and choose **More** \> **Open Cloud Shell** in the **Actions** column.
    2.  Run the `kubectl get hpa ingress-hpa` command to check the scaling status.

        If the value of REPLICAS is the same as that of MAXPODS, the scaling is completed.

        ![scaling](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7989963061/p176531.png)


## FAQ

-   What do I do if the TARGETS column displays <unknow\> after I run the kubectl get hpa command?

    Check whether the metric name is correct in the HorizontalPodAutoscaler configuration.

-   Where do I find the metrics that are supported by the HPA?

    The following table lists the commonly used metrics. For more information, see [Alibaba Cloud metrics](https://github.com/AliyunContainerService/alibaba-cloud-metrics-adapter).

    |Metric|Description|Additional parameter|
    |------|-----------|--------------------|
    |sls\_ingress\_qps|The number of requests that the ingress can process per second based on a specific routing rule.|sls.ingress.route|
    |sls\_ingress\_latency\_avg|The average latency for all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p50|The maximum latency for the fastest 50% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p95|The maximum latency for the fastest 95% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p99|The maximum latency for the fastest 99% of all requests.|sls.ingress.route|
    |sls\_ingress\_latency\_p9999|The maximum latency for the fastest 99.99% of all requests.|sls.ingress.route|
    |sls\_ingress\_inflow|The inbound bandwidth of the ingress.|sls.ingress.route|



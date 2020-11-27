# Activate Tracing Analysis on Knative

Tracing Analysis provides developers with various features for distributed application development, including trace mapping, request counting, trace discovery, and application dependency analytics. This topic describes how to activate Tracing Analysis to help developers analyze and diagnose applications on Knative.

-   [Create a managed kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed kubernetes cluster.md)
-   [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative components in a Kubernetes cluster.md)
-   You are connected to a master node in the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   Knative Serving is deployed. For more information, see [Deploy a Knative component](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy a Knative component.md).

## Procedure

1.  Deploy Istio.

    For more information, see [Deploy Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Deploy Istio.md).

    Take note of the following items:

    -   In Pilot Settings, we recommend that you set Trace Sampling Percentage to a large value such as 100 to facilitate data sampling.

        ![Deploy Istio](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9065359951/p49240.png)

    -   Select [Activate Tracing Analysis](https://tracing-analysis.console.aliyun.com/). If the service is not activated, click [Activate Now](https://tracing-analysis.console.aliyun.com/).
    -   Set **View Token** to **On** and use **zipkin** to collect data. Only the /api/v1/spans endpoint is supported. Then, select the endpoint in the region where the cluster is deployed. We recommend that you select an internal endpoint. In this example, the cluster is deployed in the China \(Shenzhen\) region.
    -   Select **Activate Tracing Analysis** and enter the endpoint.

        ![Set the endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9065359951/p49242.png)

2.  Run the following command to add the `istio-injection=enabled` label to a specified namespace. This enables automatic sidecar injection.

    After you run this command, the envoy proxy container of Istio is injected. The envoy proxy intercepts traffic and automatically reports to the tracing system. The default namespace is used in the following example:

    ```
    kubectl label namespace default istio-injection=enabled
    ```

3.  Deploy a Knative service.

    For more information, see [t474495.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative services/Deploy a Hello World application.md).

    Run the following command to access the Knative service. In this example, the Hello World service is accessed.

    ```
    curl -H "Host: helloworld-go.default.example.com" http://112.124.XX.XX
    ```

    ```
    Hello Knative!
    ```

4.  Log on to the [Tracing Analysis console](https://tracing-analysis.console.aliyun.com). Click **Applications** in the left-side navigation pane and find the application for which you want to view the tracing information.

5.  Click the application to go to the **Application Details** page. You can view the average response time of the application.


## Conclusion

We recommend that you activate Tracing Analysis on Knative. The service allows you to troubleshoot issues among applications and provides best practices on Knative.


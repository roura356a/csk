# Set up a custom domain name in Knative Serving

This topic describes how to set up a custom domain name for Knative Serving.

-   A domain name is obtained. For more information, see [Alibaba Cloud Domain Name Service](/intl.en-US/Product Introduction/What is Alibaba Cloud Domains?.md).
-   Knative is deployed in your Container Service for Kubernetes \(ACK\) cluster. For more information, see [t423002.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Knative management/Manage Knative components/Deploy Knative.md).

Knative Serving uses the default domain name `example.com` in a route. The default format of a fully qualified domain name for a Knative Serving route is `{route}.{namespace}.{default-domain}`.

## Use kubectl to modify a domain name

The following example demonstrates how to change the default domain name to a custom one in the knative-serving namespace.

1.  [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

2.  Run the following command to modify the ConfigMap named config-domain.

    ```
    kubectl edit cm config-domain --namespace knative-serving
    ```

3.  Modify the configuration file.

    Replace example.com with the custom domain name that you want to use, and save the configuration. In this example, the custom domain name is mydomain.com.

    ```
    apiVersion: v1
    data:
      mydomain.com: ""
    kind: ConfigMap
    [...]
    ```


## Deploy a Knative Service

If you have deployed a Knative Service, Knative automatically updates all Knative Services and routes based on the ConfigMap.

1.  Deploy a Knative Service. In this example, a Knative Service named helloworld-go is deployed. For more information, see [Deploy a Hello World application]().

2.  View the deployment result.

    -   For Knative 0.7, run the following command to query the domain name of the Knative Service:

        ```
        kubectl get route ${SVC_NAME} --output jsonpath="{.status.url}"| awk -F/ '{print $3}'`
        ```

    -   For Knative 0.6, run the following command to query the domain name of the Knative Service:

        ```
        kubectl get route helloworld-go --output jsonpath="{.status.domain}"
        ```

    If the following output is returned, it indicates that the custom domain name is applied:

    ```
    helloworld-go.default.mydomain.com
    ```


## Map the custom domain name to the Knative Service

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

3.  In the left-side navigation pane, choose **Knative** \> **Services**.

4.  On the Knative Services page, select the cluster and namespace where the Knative Service is deployed. Find the Knative Service that you want to manage and click **Gateway** in the Actions column.

5.  Add the IP address of the gateway to the resolution settings of the custom domain name in the Alibaba Cloud DNS console..


Run the following command to view the result:

```
curl http://helloworld-go.default.mydomain.com
```


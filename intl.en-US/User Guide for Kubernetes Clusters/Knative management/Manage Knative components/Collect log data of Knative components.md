---
keyword: [Knative components, collect logs, Docker standard output]
---

# Collect log data of Knative components

You can collect log data of Knative components and perform operations analytics and troubleshooting based on the collected log data. This topic describes how to use Log Service to collect the Docker standard output logs of Knative components.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

Knative components include:

-   knative-serving
    -   activator
    -   autoscaler
    -   autoscaler-hpa
    -   controller
    -   webhook
-   knative-eventing
    -   eventing-controller
    -   eventing-webhook

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster, or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Cluster Information** and click the **Cluster Resources** tab.

5.  On the **Cluster Resources** tab, click the hyperlink next to **Log Service Project**.

6.  In the upper-right corner of the Overview page, click **Import Data**.

7.  In the Import Data dialog box, click **Docker Standard Output - Container**.

8.  Complete the Docker standard output settings.

    For more information, see [Configure log collection](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md).

    The following sample code shows how to configure the **data source** to collect the standard output logs of the controller in the knative-serving namespace. Sample code:

    ```
    {
        "inputs": [
            {
                "detail": {
                    "IncludeEnv": {
              "SYSTEM_NAMESPACE":"knative-serving"
            },
                    "IncludeLabel": {
              "io.kubernetes.container.name": "controller"
                    },
                    "ExcludeLabel": {}
                },
                "type": "service_docker_stdout"
            }
        ]
    }
    ```

    ![Data collection](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0065359951/p127942.png)

    **Note:**

    -   In the IncludeEnv section, set SYSTEM\_NAMESPACE to the namespace where the controller is installed.
    -   In the IncludeLabel section, set io.kubernetes.container.name to the name of the controller.
9.  In the **Log Query** section, click **Try Now** to view log collection results.

    ![Query log data](../images/p127947.png)



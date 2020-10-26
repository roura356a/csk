---
keyword: [Knative system components, collect logs, Docker standard output]
---

# Collect logs of Knative system components

You can collect the logs of Knative system components and perform operational analysis and troubleshooting based on the collected logs. This topic describes how to use Log Service to collect the Docker standard output logs of Knative system components.

[Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md)

Knative system components:

-   knative-serving
    -   activator
    -   autoscaler
    -   autoscaler-hpa
    -   controller
    -   istio-webhook
    -   networking-istio
    -   webhook
-   knative-eventing
    -   eventing-controller
    -   eventing-webhook

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Cluster Information** and click the **Cluster Resources** tab.

5.  On the **Cluster Resources** tab, click the link next to **Log Service Project**.

6.  In the upper-right corner of the Overview page, click **Import Data**.

7.  In the Import Data dialog box, click **Docker Standard Output - Container**.

8.  Configure the Docker standard output settings.

    For more information, see [Configure log collection settings](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md).

    The following sample code shows how to configure the **data source** to collect the standard output logs of the controller in the knative-serving namespace.

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

    ![Data collection](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0065359951/p127942.png)

    **Note:**

    -   In the IncludeEnv section, set SYSTEM\_NAMESPACE to the namespace where the controller is installed.
    -   In the IncludeLabel section, set io.kubernetes.container.name to the name of the controller.
9.  In the **Log Query** section, click **Try Now** to view log collection results.



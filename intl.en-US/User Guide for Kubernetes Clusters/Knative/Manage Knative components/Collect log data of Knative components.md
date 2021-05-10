---
keyword: [Knative components, collect log, Docker stdout log]
---

# Collect log data of Knative components

You can collect the log of Knative components and perform operations analysis and troubleshooting based on the collected log. This topic describes how to collect the Docker stdout log of Knative components by using Log Service.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

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

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Cluster Information** and click the **Cluster Resources** tab.

5.  On the **Cluster Resources** tab, click the hyperlink next to **Log Service Project**.

6.  In the upper-right corner of the Overview page of the Log Service project, click **Import Data**.

7.  In the Import Data dialog box, click **Docker Standard Output - Container**.

8.  Complete the Docker standard output settings.

    The following content describes how to complete the **Configure Data Source** wizard page. For more information, see [Configure log collection](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md).

    The following sample code provides an example on how to configure the **data source** to collect the stdout log of controller in the knative-serving namespace. Sample code:

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

    -   In the IncludeEnv section, set SYSTEM\_NAMESPACE to the namespace where controller is installed.
    -   In the IncludeLabel section, set io.kubernetes.container.name to the name of the component, which is controller in this example.
9.  In the **Log Query** section, click **Try Now** to view the collected log.



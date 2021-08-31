---
keyword: [cost analysis, cluster]
---

# Cluster cost analysis

The cost analysis feature of Container Service for Kubernetes \(ACK\) provides analysis on resource usage and cost distribution. This provides suggestions for enterprise IT administrators on how to improve resource utilization and reduce the cost. This topic introduces the cost analysis feature and how to configure cost analysis.

-   An ACK cluster of Kubernetes 1.18.8 or later is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Prometheus Service is enabled for the created cluster. For more information, see [Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md).
-   Before you use the feature, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm) to apply to be added to a whitelist.

## Features

The following features are provided:

-   Cost analysis of cloud resources

    Aggregates the costs of all cloud resources within a Kubernetes cluster. Compares the cost of the previous day with that of the same date of the previous year. Compares the cost of the previous day with that of the day before the previous day.

-   Cost trend analysis

    Analyzes the cost trends of all cloud resources. Lists cloud resources with the highest cost contributions. This provides information that helps you reduce costs.

-   Suggestions on cost saving

    Analyzes the cost trend of a Kubernetes cluster. Provides correlation analysis between resource usage and cost contribution. Provides suggestions to reduce costs.

-   Real-time cost forecast

    Simulates cost calculation based on the real-time prices of all computing resources within a cluster. Provides hourly cost forecasts. Multiple billing methods, such as subscription, pay-as-you-go, and preemptible instances, are supported.

-   Cost allocation based on namespaces

    Allocates the cost of a cluster based on namespaces. Provides the real-time cost forecast, cost trend, and cost contribution of each namespace.

-   Optimization of application costs

    Lists the top 10 applications with the highest cost contributions. Provides the cost trends of the top 10 applications. This provides information that helps you improve the cost-effectiveness of an application.


## Configure cost analysis

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Cost Analysis**.

5.  On the Cost Analysis page, click the worker Resource Access Management \(RAM\) role **KubernetesWorkerRole-\*\*\*** of the cluster to go to the RAM console and modify the permission policy.

    ![RAM policies](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1778890261/p272320.png)

    1.  On the RAM Roles page, click the name of the **K8sWorkerRolePolicy-\*\*\*** policy.

    2.  On the Policies page, click **Modify Policy Document**.

    3.  In the Modify Policy Document panel, add the following content and then click **OK**.

        ```
                {
                    "Action": [
                        "bssapi:QueryInstanceBill"
                    ],
                    "Resource": "*",
                    "Effect": "Allow"
                },
                {
                    "Action": [
                        "ecs:DescribeSpotPriceHistory",
                        "ecs:DescribeInstances",
                        "ecs:DescribePrice"
                    ],
                    "Resource": "*",
                    "Effect": "Allow"
                }
        ```

        **Note:** To specify multiple actions, add a comma \(,\) to the end of the content of each action before you enter the content of the next action.

6.  On the Cost Analysis page, click **Install**.

    ![Install the plug-ins](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1778890261/p272328.png)

7.  On the Cost Analysis page, you can view a dashboard that provides visualized information about the cost.

    **Note:** After cost analysis is enabled, the bills are displayed at 08:00:00 \(UTC+8\) on the next day.

    ![Dashboard that provides visualized information about the cost](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1778890261/p272334.png)


## Use cost analysis

![Dashboard that provides visualized information about the cost](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1778890261/p272351.png)

The following table describes the features of the dashboard.

|No.|Feature|Description|
|---|-------|-----------|
|①|Analyze the resource waste of the cluster based on the cost trend|This figure shows the details of the cost. Daily Cost indicates the cost of the previous day. Day-on-day Ratio indicates the growth rate of the cost of the previous day. If the growth rate is in green color, it indicates that the cost of the previous day is reduced compared with the cost of the day before the previous day. If the growth rate is in red color, it indicates that the cost of the previous day is increased compared with the cost of the day before the previous day |
|②|The yellow curve indicates the resource consumption. The blue curve indicates the actual resource capacity of the cluster. In common cases, the two curves may be correlated with each other. If the two curves represent different trends, it indicates changes to the average cost of one CPU core. In this case, you can check whether resources of higher prices are used. |
|③|The cost trend within a time period.|
|④|Allocate the cost based on namespaces|A cluster may contain nodes of different specifications and billing methods. When you allocate the cost based on namespaces, you must consider the price differences of nodes that host pods in different namespaces, not only the resource consumption of different namespaces.

The cost analysis feature converts the real-time cost of each node by using alibaba-cloud-price-exporter. The cost of a namespace is calculated based on the following formula:

```
Σ (Pod resource requests/Node capacity) × Node unit price
```

This provides a precise method to calculate the cost of a namespace. However, discounts, vouchers, and subscriptions may cause the actual cost to be different from the cost calculated based on this formula. To obtain the actual cost of a namespace, you can multiply the actual cost of the cluster by the ratio of the cost of this namespace to the total cost of all namespaces. |
|⑤|Analyze the costs of cloud resources based on cost trends|A cluster may use multiple cloud resources. The costs of cloud resources vary based on the billing rules and how the cloud resources are used by the cluster. This feature provides the cost trends and contributions of different cloud resources. This provides information that helps you reduce the cost.|

## FAQ




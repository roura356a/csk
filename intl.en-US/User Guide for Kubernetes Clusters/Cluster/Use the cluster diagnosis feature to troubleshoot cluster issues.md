---
keyword: [diagnosis, node diagnosis, pod diagnosis, network diagnosis]
---

# Use the cluster diagnosis feature to troubleshoot cluster issues

Container Service for Kubernetes \(ACK\) provides the cluster diagnosis feature. This feature allows you to troubleshoot node, pod, and network issues in an ACK cluster. This topic describes how to use the cluster diagnosis feature to diagnose an ACK cluster.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The ACK cluster is running as expected.

    You can log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com), navigate to the Clusters page, and check whether the **status** of the cluster is **Running**.


## Use scenarios

You have located the pod, node, or network where the issue occurs. In this case, you can click **Node diagnosis**, **Pod diagnosis**, or **Network diagnosis** and then set the required parameters to start the diagnosis. After the diagnosis is completed, you can view the diagnostic result and suggestions.

## Limits

Only the China \(Hohhot\), China \(Chengdu\), China \(Zhangjiakou\), and China \(Heyuan\) regions support the cluster diagnosis feature.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Cluster Check**.

5.  In the left-side navigation pane of the Container Service Operation Center page, choose **Cluster Check** \> **Diagnosis**.

6.  Click **Node diagnosis**, **Pod diagnosis**, or **Network diagnosis**, and set the parameters in the panel that appears.

    The following table describes the diagnosis parameters.

    |Item|Parameter|Description|
    |----|---------|-----------|
    |**Node diagnosis**|**Node name**: Enter the name of the node that you want to diagnose. Example: `cn-hangzhou.10.160.XX.XX`.|If the status of a node is NotReady, you can enter the name of the node and diagnose the node. The system checks the cluster configurations and the status of node processes, and then lists the diagnosis items and generates the diagnostic result.|
    |**Pod diagnosis**|    -   **Pod Namespace**: Enter the namespace of the pod that you want to diagnose.
    -   **Pod Name**: Enter the name of the pod that you want to diagnose.
|You can enter the namespace and name of a pod to diagnose the pod. The system checks the pod status, pod configurations, and node that hosts the pod, and then lists the diagnosis items and generates the diagnostic result.|
    |**Network diagnosis**|    -   **Source address**: Enter the source IP address.
    -   **Destination address**: Enter the destination IP address.
    -   **Destination port**: Enter the destination port.
    -   **Enable packet tracing**: Specify whether to trace packets. The host sends TCP or UDP packets and attempts to capture the packets on the network node that uses the destination IP address. The host locates the link on which packets are dropped based on the destination that the packets actually arrive. It requires 1 to 5 minutes to complete the packet tracing.
|You can enter the source IP address, destination IP address, and destination port to diagnose the network. The system simulates user requests and provides the diagnostic result.|

    **Note:** When the system performs a diagnosis, the system runs a process on a cluster node to collect data and diagnostic result. Information about the operating system version, loads, Docker, kubelet and other components, and key error messages in the system log is collected. The system does not collect information related to the business or sensitive data.

    After you set the parameters, select **I know and agree** and click **Create diagnosis**.

7.  In the list of diagnosis records, find the diagnosis record that you want to manage and click **Diagnosis details** in the **operation** column.

    The following table describes the details about the diagnoses.

    |Item|Page|Flag|Description|
    |----|----|----|-----------|
    |**Node diagnosis**|![Node diagnosis](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1432750361/p300874.png)

|    -   Normal ![Normal](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2432750361/p300983.png): No operations are required.
    -   Warning ![Warning](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1404750361/p300985.png): Confirm the severity of the issue. Troubleshoot the issues that may cause cluster anomalies.
    -   Abnormal ![Abnormal](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1404750361/p300986.png): Troubleshoot the issues at the earliest opportunity to avoid cluster errors.
    -   Unknown ![Unknown](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1404750361/p300987.png): The diagnosis may not be completed or the diagnostic result is unknown.
|The details page of the diagnosis displays the diagnostic result, suggestions, and diagnosis items.     -   Diagnosis items with the Abnormal or Warning flag are displayed on the Troubleshoot tab.
    -   When a diagnosis item display the Abnormal flag, you can move the pointer over **Details** in the **Status** column to view details about the exception.
    -   Node diagnosis includes the **Node**, **Network**, **NodeComponent**, and **ClusterComponent** diagnosis items. The system locates the causes of node exceptions based on the status of the Elastic Compute Service \(ECS\) instance, node, cluster components, and the information collected from the node. You can view the diagnosis items on the details page of the diagnosis. |
    |**Pod diagnosis**|![Pod diagnosis](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2432750361/p300906.png)

|Pod diagnosis includes the **Pod**, **Node**, **Network**, **NodeComponent**, and **ClusterComponent** diagnosis items. The system locates the causes of pod exceptions based on the status of the pod, node, and cluster components. You can view the diagnosis items on the details page of the diagnosis.|
    |**Network diagnosis**|![Network diagnosis](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2432750361/p300935.png)

|N/A|You can view details about the network diagnosis based on the **diagnosis result**, **packet paths**, and **all possible paths**.|



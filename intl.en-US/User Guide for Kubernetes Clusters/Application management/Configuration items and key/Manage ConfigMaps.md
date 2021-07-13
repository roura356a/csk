---
keyword: [ConfigMaps, ConfigMap]
---

# Manage ConfigMaps

In the Container Service for Kubernetes \(ACK\) console, you can create a ConfigMap on the ConfigMap page or from a YAML template. You can use ConfigMaps to store non-sensitive, unencrypted configuration information. This topic describes how to manage ConfigMaps.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Create a ConfigMap

**Create a ConfigMap on the ConfigMap page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **ConfigMap** page, select the namespace to which the ConfigMap belongs from the **Namespace** drop-down list, and click **Create** in the upper-right corner.

6.  In the Create panel, enter a name for the ConfigMap, click **+ Add**, enter **keys** and **values**, and then click **OK**.

    -   **ConfigMap Name**: Required. The name of the ConfigMap. The name can contain lowercase letters, digits, hyphens \(-\), and periods \(.\). Other resource objects must reference the ConfigMap name to obtain configuration information.
    -   **ConfigMap**: Enter the names and values of the ConfigMap in key-value pairs. You can also click **Import** to create the ConfigMap from a file.
7.  Click **OK**.

    You can find the ConfigMap named aliyun-config on the ConfigMap page.

    ![ConfigMap](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4176558161/p10745.png)


**Create a ConfigMap by using a YAML template**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  On the Deployments page, click **Create from YAML** in the upper-right corner.

5.  On the page that appears, set the parameters and click **Create**.

    |Parameter|Description|
    |---------|-----------|
    |Sample Template|You can select Custom from the drop-down list and configure the ConfigMap in YAML syntax. You can also select **Resource - ConfigMap** from the drop-down list. If you select Resource - ConfigMap, the ConfigMap is named aliyun-config and contains the following variable files: `game.properties` and `ui.properties`. You can modify the ConfigMap to meet your requirements.|

6.  Click **Create**.

    After the ConfigMap named aliyun-config is created, you can find it on the ConfigMap page.


## View a ConfigMap

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the ConfigMap page, find the ConfigMap that you want to view and click its name.

6.  On the details page of the ConfigMap, you can view details about the ConfigMap. You can also view the key-value pairs in the ConfigMap.


## Modify a ConfigMap

If you modify a ConfigMap, the applications that use this ConfigMap are affected.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the ConfigMap page, find the ConfigMap that you want to modify and click **Edit** in the Actions column.

6.  In the Edit panel, modify the **keys** and **values** based on your requirements and click **OK**.

    **Note:** You can also modify the keys and values in the YAML file. On the **ConfigMap** page, click **Edit YAML** in the Actions column. In the **View in YAML** panel, modify the keys and values based on your requirements and click **OK**.


## Delete a ConfigMap

You can delete a ConfigMap that is no longer in use.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the ConfigMap page, find the ConfigMap that you want to delete and click **Delete** in the Actions column.

6.  In the Confirm message, click **OK**.



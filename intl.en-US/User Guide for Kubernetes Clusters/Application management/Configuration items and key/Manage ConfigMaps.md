# Manage ConfigMaps

In the Container Service for Kubernetes \(ACK\) console, you can create a ConfigMap on the ConfigMap page or from a YAML template. You can use ConfigMaps to store configuration information that does not need to be kept confidential. This topic describes how to manage ConfigMaps.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

## Create a ConfigMap

**Create a ConfigMap on the ConfigMap page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **ConfigMap** page, select the namespace to which the ConfigMap belongs from the **Namespace** drop-down list, and click **Create** in the upper-right corner of the page.

6.  In the Create panel, enter a name for the ConfigMap, click **+ Add** to enter keys and values, and then click **OK**.

    -   **ConfigMap Name**: Required. The name of the ConfigMap. The name can contain lowercase letters, digits, hyphens \(-\), and periods \(.\). Other resource objects must reference the ConfigMap name to obtain configuration information.
    -   **ConfigMap**: Enter configuration information in key-value pairs. You can also click **Import** to create the ConfigMap from a file.
7.  Click **OK**.

    After the ConfigMap is created, you can find the newly created ConfigMap named test-config on the ConfigMap page.

    ![ConfigMap](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4176558161/p10745.png)


**Create a ConfigMap from a YAML template**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

4.  On the Deployments tab, click **Create from Template**.

5.  On the page that appears, set the parameters and click **Create**.

    |Parameter|Description|
    |---------|-----------|
    | | |
    |Namespace|Select a namespace for the ConfigMap. A ConfigMap is a Kubernetes resource object and must be scoped to a namespace.|
    |Sample Template|You can select Custom from the drop-down list and configure the ConfigMap in YAML syntax. You can also select **Resource - ConfigMap** from the drop-down list. If you select Resource - ConfigMap, the ConfigMap is named aliyun-config and contains the following variable files: `game.properties` and `ui.properties`. You can modify the ConfigMap to meet your requirements.|

    ![Create a ConfigMap from a YAML template](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8545359951/p10747.png)

6.  Click **Create**.

    After the ConfigMap named aliyun-config is created, you can find it on the ConfigMap page.

    ![ConfigMap](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9545359951/p10748.png)


## View a ConfigMap

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **ConfigMap** page, find the ConfigMap that you want to view and click its name.

6.  On the details page of the ConfigMap, you can view details about the ConfigMap. You can also view the key-value pairs in the ConfigMap.

    ![ConfigMap details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0645359951/p40653.png)


## Modify a ConfigMap

If you modify a ConfigMap, the applications that use this ConfigMap will be affected.

**Modify a ConfigMap on the ConfigMap page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **ConfigMap** page, find the ConfigMap that you want to modify and click **Edit** in the Actions column.

6.  In the **Edit** panel, modify the keys and values based on your requirements and click **OK**.

    **Note:** You can also modify the keys and values in the YAML file. On the **ConfigMap** page, click **Edit YAML** in the Actions column. In the **View in YAML** panel, modify the keys and values based on your requirements and click **OK**.


1.  In the left-side navigation pane, click **Clusters**.

2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

3.  In the left-side navigation pane of the details page, choose **Configurations** \> **Secrets**.

4.  

5.  

    ![Update a ConfigMap](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9031674161/p10759.png)


## Delete a ConfigMap

You can delete a ConfigMap that is no longer in use.

**Delete a ConfigMap on the ConfigMap page**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **ConfigMap** page, find the ConfigMap that you want to delete and click **Delete** in the Actions column.

6.  In the **Confirm** message, click **OK**.


**Delete a ConfigMap on the Kubernetes dashboard**

1.  In the left-side navigation pane, click **Clusters**.

2.  On the Clusters page, find the cluster where the ConfigMap that you want to delete is stored, and choose **More** \> **Dashboard** in the **Actions** column.

3.  In the left-side navigation pane of the Kubernetes dashboard, choose **Config and Storage** \> **Config Maps**. On the Config Maps page, find the ConfigMap that you want to delete and choose **Actions** \> **Delete** on the right side of the page.

4.  In the **Delete a Config Map** message, click **DELETE**.



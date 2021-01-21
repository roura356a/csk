# Create a ConfigMap

This topic describes how to create a ConfigMap in the Container Service for Kubernetes \(ACK\) console. You can use ConfigMaps to store non-sensitive and unencrypted configuration information.

## Create a ConfigMap on the ConfigMap tab

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Configurations**.

5.  At the top of the page, select a **cluster** and **namespace** to create the ConfigMap. Click the **ConfigMap** tab. In the upper-right corner of the **ConfigMap** tab, click **Create**.

6.  In the Create pane, enter a name for the ConfigMap, click **+ Add** to enter keys and values, and then click **OK**.

    -   **ConfigMap Name**: Required. The name of the ConfigMap. The name can contain lowercase letters, digits, hyphens \(-\), and periods \(.\). Other resource objects must reference the ConfigMap name to obtain configuration information.
    -   **ConfigMap**: Enter configuration information in key-value pairs. You can also click **Import** to create the ConfigMap from a file.
7.  Click **OK**.

    After the ConfigMap is created, you can find the newly created ConfigMap named test-config on the ConfigMap tab.

    ![Configuration file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8545359951/p10745.png)


## Create a ConfigMap from a template

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

3.  In the left-side navigation pane, click **Workloads**.

4.  On the Deployments tab, click **Create from Template**.

5.  On the page that appears, set the parameters and click **Create**.

    |Parameter|Description|
    |---------|-----------|
    |Cluster|Select the cluster for which you want to create the ConfigMap.|
    |Namespace|Select a namespace for the ConfigMap. A ConfigMap is a Kubernetes resource object and must be scoped to a namespace.|
    |Sample Template|You can select Custom from the drop-down list and enter a custom ConfigMap in YAML syntax. You can also select the sample template **Resource - ConfigMap template** from the drop-down list. In the sample template, the ConfigMap is named aliyun-config and contains the following variable files: `game.properties` and `ui.properties`. You can modify the ConfigMap based on your requirements.|

    ![Create from a template](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8545359951/p10747.png)

6.  Click **Create**.


After the ConfigMap is created, you can find the newly created ConfigMap named aliyun-config ConfigMap on the ConfigMap tab.

![Configuration file](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9545359951/p10748.png)


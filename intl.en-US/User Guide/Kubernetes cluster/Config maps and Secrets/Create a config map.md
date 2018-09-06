# Create a config map {#concept_zrs_jjn_vdb .concept}

In the Container Service console, you can create a config map on the Config Maps page or by using a template.

## Create a config map on Config Maps page {#section_zlw_njn_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Application** \> **Config Maps** in the left-side navigation pane.
3.  Select the cluster from the Cluster drop-down list. Click **Create** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/153622186710742_en-US.png)

4.  Complete the settings and then click **OK**.

    -   **Namespace**: Select the namespace to which the config map belongs. Config map is a Kubernetes resource object that must be applied to the namespace.
    -   **Config Map Name**: Enter the config map name, which can contain lowercase letters, numbers, hyphens \(-\), and periods \(.\). The name cannot be empty. Other resource objects must reference the config map name to obtain the configuration information.
    -   **Configuration:** Enter the Variable Name and the Variable Value. Then, click **Add** on the right. You can also click **Edit**, complete the configuration in the displayed dialog box, and click **OK**.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/153622186710743_en-US.png)

    In this example, configure the variables enemies and lives to pass the parameters aliens and 3 respectively.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/153622186710744_en-US.png)

5.  You can view the config map test-config on the Config Maps page after clicking **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/153622186710745_en-US.png)


## Create a config map by using a template {#section_u3g_mjn_vdb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  Under Kubernetes, click **Application** \> **Deployment** in the left-side navigation pane.
3.  Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/153622186710746_en-US.png)

4.  On the Deploy templates page, complete the settings and then click **DEPLOY**.

    -   **Clusters:** Select the cluster in which the config map is to be created.
    -   **Namespace:** Select the namespace to which the config map belongs. Config map is a Kubernetes resource object that must be applied to the namespace.
    -   **Resource Type**: You can write your own config map based on the Kubernetes YAML syntax rules, or select the sample template **resource-ConfigMap**. In the sample template, the config map is named as aliyun-config and includes two variable files `game.properties` and`ui.properties`. You can make modifications based on the sample template. Then, click **DEPLOY**.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/153622186810747_en-US.png)

5.  After the successful deployment, you can view the config map aliyun-config on the Config Maps page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/153622186810748_en-US.png)



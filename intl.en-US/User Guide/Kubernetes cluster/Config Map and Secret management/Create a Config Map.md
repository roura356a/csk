# Create a Config Map {#concept_zrs_jjn_vdb .concept}

In the Container Service console, you can create a Config Map on the Config Maps page or by using a template.

## Create a Config Map on Config Maps page {#section_zlw_njn_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Configuration** \> **Config Maps**.
3.  Select the target cluster and namespace. Then, in the upper-right corner, click **Create**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/156275172610742_en-US.png)

4.  Complete the settings and then click **OK**.

    -   **Namespace**: Select the namespace to which the Config Map belongs. Config Map is a Kubernetes resource object that must be applied to the namespace.
    -   **Config Map Name**: Enter the Config Map name, which can contain lowercase letters, numbers, hyphens \(-\), and periods \(.\). The name cannot be empty. Other resource objects must reference the Config Map name to obtain the configuration information.
    -   **Configuration:** Enter the Variable Name and the Variable Value. Then, click **Add** on the right. You can also click **Edit**, complete the configuration in the displayed dialog box, and click **OK**.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/156275172710743_en-US.png)

    In this example, configure the variables enemies and lives to pass the parameters aliens and 3 respectively.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/156275172710744_en-US.png)

5.  You can view the Config Map test-config on the Config Maps page after clicking **OK**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/156275172710745_en-US.png)


## Create a Config Map by using a template {#section_u3g_mjn_vdb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**.
3.  Click **Create by template** in the upper-right corner.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/156275172710746_en-US.png)

4.  On the Deploy templates page, complete the settings and then click **DEPLOY**.

    -   **Clusters:** Select the cluster in which the Config Map is to be created.
    -   **Namespace:** Select the namespace to which the Config Map belongs. Config map is a Kubernetes resource object that must be applied to the namespace.
    -   **Resource Type**: You can write your own Config Map based on the Kubernetes YAML syntax rules, or select the sample template **resource-ConfigMap**. In the sample template, the Config Map is named as aliyun-config and includes two variable files `game.properties` and `ui.properties`. You can make modifications based on the sample template. Then, click **DEPLOY**.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/156275172710747_en-US.png)

5.  After the deployment, you can view the Config Map aliyun-config on the Config Maps page.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15755/156275172710748_en-US.png)



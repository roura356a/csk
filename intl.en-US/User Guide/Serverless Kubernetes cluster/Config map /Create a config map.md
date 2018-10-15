# Create a config map {#task_sfq_bh1_ydb .task}

In the Container Service console, you can create a config map on the Config Maps page.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Application** \> **Config Maps** in the left-side navigation pane. 
3.  On the Config Maps page, select the cluster and namespace, and click **Create**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16496/153959057510307_en-US.png)

4.  Complete the configuration and then click **OK**. 

    -   **Namespace**: Select the namespace to which the config map belongs. Config map is a Kubernetes resource object that must be applied to the namespace.
    -   **Config Map Name**: Enter the config map name, which can contain lowercase letters, numbers, hyphens \(-\), and periods \(.\). The name cannot be empty. Other resource objects must reference the config map name to obtain the configuration information.
    -   **Configuration**: Enter the Variable Name and the Variable Value. Then, click **Add** on the right. You can also click **Edit**, complete the configuration in the displayed dialog box, and click **OK**.
    In this example, configure the variables enemies and lives to pass the parameters aliens and 3 respectively.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16496/153959057510308_en-US.png)

5.  You can view the config map test-config on the Config Maps page after clicking **OK**. 


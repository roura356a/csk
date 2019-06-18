# Create a Config Map {#task_sfq_bh1_ydb .task}

This topic describes how to create a Config Map by using the Container Service console.

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Configuration** \> **Config Maps**.
3.  Select the target cluster and namespace, and then click **Create**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16496/156082199110307_en-US.png)

4.  Complete the configuration and then click **OK**. 

    -   **Namespace**: Select the namespace to which the Config Map belongs. Config Map is a Kubernetes resource object that must be applied to the namespace.
    -   **Config Map Name**: Enter the Config Map name, which can contain lowercase letters, numbers, hyphens \(-\), and periods \(.\). The name cannot be empty. Other resource objects must reference the Config Map name to obtain the configuration information.
    -   **Configuration**: Enter the Variable Name and the Variable Value. Then, click **Add** on the right. You can also click **Edit**, complete the configuration in the displayed dialog box, and click **OK**.
    In this example, configure the variables enemies and lives to pass the parameters aliens and 3 respectively.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16496/156082199110308_en-US.png)

5.  You can view the Config Map test-config on the Config Maps page after clicking **OK**.


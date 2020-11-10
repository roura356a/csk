# Create an orchestration template

This topic describes how to use multiple methods to create an orchestration template in the Container Service for Kubernetes \(ACK\) console.

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **Orchestration Templates**. In the upper-right corner of the Templates page, click **Create**.

3.  In the Create dialog box, configure the template and click **Save**. In the example, a Tomcat application template that contains a Deployment and a Service is created.

    -   **Name**: The name of the template.
    -   **Description**: The description of the template. This parameter is optional.
    -   **Template**: Configure the template based on YAML syntax. The template can contain multiple resource objects that are separated with `---`.
    ![Configure an orchestration template](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5165359951/p8820.png)

4.  After the template is created, you are redirected to the Templates page. On the **My Templates** tab, you can find the newly created template.

    ![My templates](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5165359951/p8824.png)

5.  You can also create an orchestration template by performing the following steps. In the left-side navigation pane of the ACK console, click Clusters. On the Clusters page, find and click the name of the cluster in which you want to create an orchestration template or click **Applications** in the **Actions** column. In the upper-right corner of the **Deployments** tab, click **Create from Template**. Select the built-in template that you want to use and save the template.

    1.  Select a built-in template and click **Save Template**.

    2.  In the Save Template dialog box, specify the name, description, and template content. Click **Save** to save the template.

        **Note:** You can modify the built-in template based on your requirements.

    3.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**. On the **My Templates** page, you can find the newly created template.

        ![The newly created template](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5165359951/p8831.png)


on the **My Templates** tab, You can use a template to quickly create an application.


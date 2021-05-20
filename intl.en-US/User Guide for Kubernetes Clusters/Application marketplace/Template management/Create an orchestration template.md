---
keyword: Create an orchestration template
---

# Create an orchestration template

You can create an orchestration template by using multiple methods and use the template to deploy applications. This topic describes how to create an orchestration template.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**.

3.  In the upper-right corner of the Templates page, click **Create**.

4.  In the **Create** dialog box, configure the template and click **Save**. In this example, a template that contains a Deployment and a Service is created for a Tomcat application.

    -   **Name**: Specify a name for the template.
    -   **Description**: Specify a description for the template. This parameter is optional.
    -   **Template**: Configure the template based on the YAML syntax. The template can contain multiple resource objects that are separated with `---`.
    After the template is created, you are redirected to the Templates page. On the **My Templates** tab, you can find the newly created template.

5.  You can save a template provided by Container Service for Kubernetes \(ACK\) as an orchestration template.

    1.  In the left-side navigation pane of the ACK console, click **Clusters**.

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

    5.  In the upper-right corner of the Deployments page, click **Create from Template**.

    6.  Select a template and click **Save Template**.

    7.  In the **Save Template** dialog box, specify the name, description, and content of the template. Then, click **Save**.

        **Note:** You can modify the template based on your requirements.


On the **My Templates** tab, you can use a template to deploy an application.


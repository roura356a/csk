---
keyword: [create, orchestration template, ACK]
---

# Create an orchestration template

Orchestration templates define and describe a set of resources. You can use orchestration templates to create applications. This topic describes how to create an orchestration template.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**.

3.  On the Templates page, click **Create** in the upper-right corner.

4.  In the **Create** dialog box, configure the template and click **Save**.

    -   **Name**: Specify a name for the template.
    -   **Description**: Specify a description for the template. This parameter is optional.
    -   **Template**: Configure the template that conforms to the YAML syntax.

        **Note:**

        -   The template can contain multiple resource objects that are separated with `---`.
        -   You can add values in the format of `${params}` to the YAML file, for example, `app: ${nginx}`. The value is automatically identified as a parameter when you use the template to create an application.
    After the template is created, you are redirected to the Templates page. On the **My Templates** tab, you can find the created template.

5.  You can save a template provided by Container Service for Kubernetes \(ACK\) as an orchestration template.

    1.  In the left-side navigation pane of the ACK console, click **Clusters**.

    2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    3.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

    4.  On the Deployments page, click **Create from YAML** in the upper-right corner.

    5.  Select a template and click **Save Template**.

    6.  In the **Save Template** dialog box, specify the name, description, and content of the template. Then, click **Save**.

        **Note:** You can modify the template based on your requirements.


On the **My Templates** tab, you can specify a template to create an application.


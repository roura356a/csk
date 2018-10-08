# Create an orchestration template {#task_gtg_2k3_r2b .task}

You can use multiple methods to create orchestration templates through the Container Service console.

1.  Log on to the [Container Service console](https://cs.console.aliyun.com). 
2.  Under Kubernetes, click **Store** \> **Orchestration Templates** in the left-side navigation pane. Click **Create** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17051/15389854028819_en-US.png)

3.  In the displayed dialog box, configure the orchestration template, and then click **Save**. In this example, build a tomcat application template that contains a deployment and a service. 

    -   **Name**: Set the template name.
    -   **Description**: Enter the description for the template. This parameter is optional.
    -   **Template**: Configure the template that conforms to Kubernetes yaml syntax rules. The template can contain multiple resource objects that are separated by `---`.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17051/15389854028820_en-US.png)

4.  After the template is created, the Template List page is displayed. You can see the template under **My template**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17051/15389854028824_en-US.png)

5.  You can also click **Application** \> **Deployment** in the left-hand navigation pane, and click **Create by template** to enter the Deploy templates page. Save one of orchestration templates built-in Container Service as your custom template. 
    1.  Select a built-in template and click **Save Template**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17051/15389854028826_en-US.png)

    2.  In the displayed dialog box, configure the name, description, and template. After completing the configurations, click **Save**. 

        **Note:** You can modify the built-in template.

    3.  Click **Store** \> **Orchestration Template**, the created template is displayed under **My Template**. 

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17051/15389854028831_en-US.png)


You can quickly create an application by using the orchestration template under **My Template**.


---
keyword: [trigger, orchestration template, update applications]
---

# Use a trigger to enable automatic application update

You can use triggers provided by Application Center to remotely deploy or redeploy applications. This topic describes how to use a trigger to enable automatic application update.

An application is created. For more information, see [Create an application from an orchestration template](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application management/Create an application from an orchestration template.md).

In this topic, the image version used by the application specifies that the red color appears on the application page. You can change the image version to one that specifies the blue color. Then, use a trigger to update the application. After the application is updated, you can access the application by using your browser to check whether the color is changed. The result indicates whether the application is successfully updated by the trigger. If the color that appears on the application page is changed to blue, it indicates that the image version of the application is changed. This also indicates that the automatic application update is successfully performed by the trigger.

## Update the orchestration template

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**.

3.  On the Templates page, find the orchestration template that is used to deploy the application and click **Details** below the template name.

4.  Change the image registry to `registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:blue` in the template. Click **Save**.


## Update the application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Application Center page, find the application and click **Details** in the **Actions** column.

4.  On the application details page, click **Trigger** in the upper-right corner.

5.  In the **Trigger** panel, click **Create**. After the webhook URL of the trigger appears, click **Copy**.

6.  Run the following command to use the trigger to redeploy the application:

    ```
    curl <URL of the trigger>
    ```

    Expected output:

    ```
    null
    ```


## Verify the result

1.  Gets the endpoint of the application.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Network** \> **Ingresses**.

    5.  View the endpoint of the demo on the routing page.

2.  Enter the following in the hosts file.

    ```
    <endpoint> app.demo.example.com 
    ```

3.  Enter app.demo.example.com in the address bar of your browser and press Enter to access the application.

    If the page appears as shown in the following figure, it indicates that the application is updated.

    ![Blue](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7709128261/p264043.png)


**Related topics**  


[Trigger overview](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Triggers/Trigger overview.md)

[t2068340.md\#]()


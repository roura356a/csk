---
keyword: [trigger, container image, automatically update applications]
---

# Create a trigger to automate application updates when the base image is updated

You can create triggers for each repository in Container Registry. After you create a trigger for a repository, a notification is sent to you when a container image in the repository is updated. When the base image of an application is updated and the update matches the trigger conditions, the updated base image is pulled to redeploy the application. This topic describes how to automate Application Center to pull an updated base image and use the image to redeploy the application by creating a trigger.

A container image is pushed to a repository in Container Registry. For more information, see [Use a Container Registry Enterprise Edition instance to push and pull images]().

## Step 1: Create an application

For more information, see [Deploy an application in Application Center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Application management/Deploy an application in Application Center.md).

## Step 2: Generate the webhook URL of a trigger in Application Center

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Applications page, click the name of the application that you created.

4.  On the application details page, click **Trigger** in the upper-right corner.

5.  In the **Trigger** panel, click **Create**. After the webhook URL of the trigger appears, click **Copy**.

    **Note:** We recommend that you keep the webhook URL confidential.


## Step 3: Create a trigger in Container Registry

1.  Log on to the [Container Registry console](https://cr.console.aliyun.com).

2.  In the top navigation bar, select a region.

3.  In the left-side navigation pane, click **Instances**.

4.  On the Instances page, click the required Container Registry Enterprise Edition instance.

5.  On the management page of the Container Registry Enterprise Edition instance, , choose **Repositories** \> **Repositories**.

6.  On the Repositories page, click the name of the repository that is used when you created the application in [Step 1](#section_nmr_hga_zoo).

7.  On the repository details page, click **Trigger**. Then, click **Create**.

8.  In the **Create Trigger** dialog box, specify the name of the trigger, enter the webhook URL of the trigger generated in [Step 2](#section_gc6_cw8_7r0), and set **Trigger** to **All**. Then, click **Confirm**.

    The following trigger modes are supported: **All**, **By RegExp**, and **By Tags**. The following section describes the trigger modes:

    -   All: Each time an image is updated, Application Center pulls the updated image.
    -   By RegExp: A regular expression is used to filter image updates. Application Center pulls an image only if the image is updated and the new image version matches the regular expression.
    -   By Tags: Tags are used to filter image updates. Application Center pulls an image only if the image is updated and the new image version matches one of the specified tags.
    **Note:** If Application Center fails to pull an image, you can go to the **Trigger** page to find the trigger and click **Access Log** in the Actions column. In the **Access Log** message, check the cause of exceptions from the response bodies.


## Step 4: Redeploy the application by using the updated container image

When a container image in the repository is updated, the updated image is automatically pulled and used to redeploy the application.

1.  Run the following command to log on to the repository that is used to create the application in [Step 1](#section_nmr_hga_zoo).

    **Note:** If the instance of Container Registry Enterprise Edition is created in the China \(Hangzhou\) region, you must set <the region where the instance of Enterprise Edition is created\> to hangzhou in the command.

    ```
    docker login --username=<username used to log on to Container Registry> <name of the instance of Container Registry Enterprise Edition>-registry.cn-<the region where the instance of Container Registry Enterprise Edition is created>.cr.aliyuncs.com
    ```

2.  Run the following command to add a tag to the image:

    ```
    docker tag registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:green liusheng-registry.cn-beijing.cr.aliyuncs.com/liusheng/rollouts-demo:green
    ```

3.  Run the following command to push the new version of the container image to the repository that is used when you created the application in [Step 1](#section_nmr_hga_zoo).

    ```
    docker push liusheng-registry.cn-beijing.cr.aliyuncs.com/liusheng/rollouts-demo:green
    ```


## Verify the result

1.  View the orchestration template.

    After the container image is updated, Application Center pulls the updated image and updates the orchestration template of the application.

    1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**.

    3.  On the Templates page, find the template that you created in [Step 1](#section_nmr_hga_zoo) and click **Details**. On the **Details** page, the updated image is used in the template.

2.  Get the endpoint of the application.

    1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

    5.  On the **Ingresses** page,view the endpoint of the demo.

3.  Enter the following in the hosts file.

    ```
    <endpoint> app.demo.example.com 
    ```

4.  Enter app.demo.example.com in the address bar of your browser.

    If the page shown in the following figure appears in your browser, the application is updated.

    ![Verify the result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2683779161/p263738.png)



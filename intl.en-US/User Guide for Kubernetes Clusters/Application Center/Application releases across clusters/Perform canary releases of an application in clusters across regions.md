---
keyword: [clusters across regions, canary release]
---

# Perform canary releases of an application in clusters across regions

Container Service for Kubernetes \(ACK\) allows you to release an application to clusters in different regions. You can also perform canary releases of an application among multiple clusters based on regions. This topic describes how to perform a canary release based on regions after the orchestration template is updated. In this example, Cluster A in the China \(Beijing\) region still uses the original application version, and Cluster B in the China \(Hangzhou\) region uses the new application version.

A cluster is created in the China \(Beijing\) region and another is created in the China \(Hangzhou\) region. These clusters must have access to the Internet. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

In this topic, the cluster in the China \(Beijing\) region is regarded as Cluster A. The cluster in the China \(Hangzhou\) region is regarded as Cluster B. An application is deployed in both clusters. The application specifies that the red color appears on the application page. In the canary release, the application in Cluster A stays unchanged, and the application in Cluster B is updated based on the new orchestration template that specifies that the green color appears on the application page.

## Step 1: Deploy an application in Cluster A and Cluster B

In this topic, an application is deployed in both clusters. The color that appears on the application page is red. For more information, see [Use an orchestration template to deploy an application to multiple clusters](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application releases across clusters/Use an orchestration template to deploy an application to multiple clusters.md).

## Step 2: Update the orchestration template

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **Orchestration Templates**.

3.  On the Templates page, find the orchestration template that is used to deploy the application and click **Details** below the template name.

4.  Change the image registry to `registry.cn-hangzhou.aliyuncs.com/acs/rollouts-demo:green` in the template. Click **Create**.


## Step 3: Configure a canary release of the application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Application Center page, find the application and click **Create Change** in the **Actions** column.

4.  In the **Create Change** step, click **Next**.

5.  In the **Release Configurations** step, select Cluster B in the **Cluster** section and click **Release Now**.

6.  In the **Confirm Release** message, click **OK**.


## Verify the result

1.  Obtain the endpoint of the application.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find Cluster A and click **Details** in the **Actions** column.

    4.  In the left-side navigation pane of the details page, choose **Network** \> **Ingresses**.

    5.  In the Ingresses page, view the endpoint of the demo.

2.  Add the following content to the hosts file:

    ```
    <Endpoint> app.demo.example.com 
    ```

3.  Enter app.demo.example.com in the address bar of your browser and press Enter to access the application.

    The page appears as shown in the following figure.

    ![red ](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9817128261/p263871.png)

4.  Delete the endpoint added in [Step 2](#step_syl_k2b_1i0) from the hosts file. Perform [Step 1](#step_n3d_83b_w1m) to [Step 3](#step_crz_r6g_ocn) again to access the application in Cluster B. The page appears as shown in the following figure.

    ![Verify the result](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2683779161/p263738.png)

    The result shows that the red color appears on the application page when you access the application in Cluster A, and the color is changed to green when you access the application in Cluster B. This indicates that the canary release of the application is successfully performed among multiple clusters based on regions.


**Related topics**  


[Use an orchestration template to deploy an application to multiple clusters](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application releases across clusters/Use an orchestration template to deploy an application to multiple clusters.md)


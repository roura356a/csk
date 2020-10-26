# Create an application in the Kubernetes dashboard

You can create an application in the Kubernetes dashboard.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  The Clusters page appears. Find the cluster that you want to manage and choose **More** \> **Dashboard** in the **Actions** column of the cluster.

4.  On the Overview page, click **CREATE** in the upper-right corner of the page.

5.  On the **Create Resource** page, configure the application that you want to create.

    You can use one of the following methods to create the application:

    -   **CREATE FROM TEXT INPUT**: Enter the orchestration code in the YAML or JSON format to create an application. You must understand the required orchestration format. The following sample code shows the YAML template.
    -   **CREATE FROM FILE**: Upload an existing YAML or JSON configuration file to create an application.
    -   **CREATE AN APP**: Complete the following configurations to create an application.

        |Parameter|Description|
        |---------|-----------|
        |**App name**|The name of the application. In this example, `NGINX` is used.|
        |**Container image**|The URL of the image. In this example, a Docker [NGINX](https://hub.docker.com/_/nginx/) image is used.|
        |**Number of pods**|The number of pods that are included in the application.|
        |**Service**|Valid values: **External** and **Internal**.        -   **External** specifies a service that can be accessed from outside the cluster.
        -   **Internal** specifies that a service that can be accessed from within the cluster. |
        |**Advanced options**|Click **SHOW ADVANCED OPTIONS** to configure advanced settings such as labels and environment variables. These settings are used to balance network traffic that is distributed to three pods.|

6.  Click UPLOAD or **DEPLOY** to deploy the pods and services.

    You can also click **SHOW ADVANCED OPTIONS** to configure other parameters.


After you click UPLOAD or **DEPLOY**, you can view the services and containers of the application.

In the left-side navigation pane, click **Pods**. The icon on the left side of each pod indicates the status of the pod. ![Icons](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9966073061/p11100.png) indicates that the pod is being deployed. ![Deploy](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9966073061/p11101.png) indicates that the pod has been deployed.

![Deployment completed](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0076073061/p11103.png)


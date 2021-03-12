# Use an image secret

Container Service for Kubernetes \(ACK\) allows you to use image secrets in the ACK console. You can create an image secret or use an existing image secret.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A private image repository is created and your image is uploaded to the repository. An image repository in Alibaba Cloud Container Registry is used in the following example. For more information, see [Create an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Create an image.md).

When you create an application by using a private image, you must set a secret for the image to secure the image. In the ACK console, you can specify the authentication information of the private image repository as a secret of the docker-registry type. This secret applies to the specified Kubernetes cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** tab, set the namespace and click **Create from Image**.

6.  On the **Basic Information** wizard page, set the required parameters. For more information, see [Step 1: Configure basic settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

7.  Configure containers.

    This topic describes how to configure an image secret. For more information about the container configuration, see [Step 2: Configure the containers](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

    1.  On the **Container** wizard page, enter the address of the private image in the **Image Name** field. The address is in the following format: `domainname/namespace/imagename`.

        **Note:** Public images do not require secrets.

    2.  Enter the version of the image in the Image Version field.

        A Tomcat private image is used in this example.

        ![Configure container parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13583.png)

    3.  Click **Set Image Pull Secret**, and create a secret or select an existing secret in the dialog box that appears.

        -   You can select **New Secret**. If you select New Secret, set the following parameters:

            |Parameter|Description|
            |---------|-----------|
            |**Name** \(regsecret\)|The key name of the secret. The name can be customized.|
            |**Repository Domain** \(docker-server\)|The address of the specified Docker registry. If you use an image repository in Alibaba Cloud Container Registry, the address of the image repository is automatically displayed.|
            |**Username** \(docker-username\)|The username of the Docker repository. If you use an image repository in Alibaba Cloud Container Registry, your Alibaba Cloud account name is used as the username.|
            |**Password** \(docker-password\)|The password that is used to log on to the Docker repository. If you use an image repository in Alibaba Cloud Container Registry, the password is the independent logon password of Container Registry.|
            |**Email** \(docker-email\)|The email address. This parameter is optional.|

            Click **OK**. The newly created secret appears on the page.

            ![Set a secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13585.png)

        -   You can also select **Exist Secret**. You can use commands or YAML files to create image secrets. For more information, see [How do I use private images in Kubernetes clusters?]() and [Create an application from a private image repository](/intl.en-US/Quick Start/Advanced operations/Create an application from a private image repository.md).

            ![Existing secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13586.png)

8.  Specify other configurations as required. Then, click **Create**.

    For more information about advanced configurations, see [Step 3: Configure advanced settings](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Deploy a stateless application from an image.md).

9.  In the left-side navigation pane of the ACK console, click **Clusters**.

10. On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

11. In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

12. On the **Deployments** page, check the running status of the tomcat application.

    The following figure shows that the tomcat application runs as normal. This indicates that the Tomcat private image is used with the image secret.



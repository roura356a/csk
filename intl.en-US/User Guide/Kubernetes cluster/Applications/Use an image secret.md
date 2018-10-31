# Use an image secret {#task_gdn_w2x_kfb .task}

Container Service Kubernetes clusters support using image secrets through the web interface. You can create an image secret and use an existing image secret.

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have built a private image repository and uploaded your image to the repository. In this example, use Alibaba Cloud Container Registry. For more information, see [Use a private image repository to create an application](../reseller.en-US/quickstart/Advanced operations/Use a private image repository to create an application.md#).

When you use a private image to create an application, you have to configure a secret for the image to secure the image. In the Container Service console, you can deliver the identity authentication information of the private image repository to Kubernetes through a secret of the docker-registry type.

1.  登录[容器服务管理控制台](https://partners-intl.console.aliyun.com/#/cs)。 
2.  Under the Kubernetes menu, click **Application** \> **Deployment** in the left-side navigation pane, and then click **Create by Image** in the upper-right corner. 
3.  Configure **Name**, **Cluster**, **Namespace**, **Replicas**, and **Type**. The configured value of the replicas parameter specifies the number of pods contained in the application. Click **Next**. 

    **Note:** In this example, select the **Deployment** type.

    If you do not configure **Namespace**, the system uses the default namespace by default.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23476/154095167213581_en-US.png)

4.  Configure containers. 

    **Note:** This example describes only the configuration of the container image secret. For more information about container configuration, see [Create a deployment application by using an image](reseller.en-US/User Guide/Kubernetes cluster/Applications/Create a deployment application by using an image.md#).

5.  On the container configuration page, configure the image name first. Enter the private image address in the **Image Name** box. The format is `domainname/namespace/imagename`. 

    **Note:** Public images do not require image secrets.

6.  In the image version box, enter the private image address version. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23476/154095167213583_en-US.png)

7.  Click **Image pull secret**. 
    -   Select **Create secret**.

        -   Name: Specifies the secret name. You can define it by yourself.
        -   Repository Domain Name: Specified the Docker repository address. If you enter the Alibaba Cloud Container Service image repository in the image name box, the system automatically adds the repository address by default.
        -   Username: Specifies the user name of the Docker repository. If you use Alibaba Cloud Container Registry, the username is your Alibaba Cloud account name.
        -   Password: Specifies the logon password of the Docker repository. If you use Alibaba Cloud Container Registry, the password is the independent logon password for Container Registry.
        -   Email: Specifies an email address. This is optional.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23476/154095167213584_en-US.png)

        Click **OK**. The created secrete is displayed on the page.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23476/154095167213585_en-US.png)

    -   You can also click **Exist secret**. You can pre-create a container image secret by using command lines or a YAML file. For information, see [How to use private images in Kubernetes clusters](../reseller.en-US/problem/How to use private images in Kubernetes clusters.md#) and [Use a private image repository to create an application](../reseller.en-US/quickstart/Advanced operations/Use a private image repository to create an application.md#).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23476/154095167213586_en-US.png)

8.  After you complete the container configuration, click **Next**. 
9.  Follow the page guide to complete other configurations, and then click **Create**. 
10. Click **Application** \> **Deployment** in the left-side navigation pane, and select the cluster and namespace in which the application is created to view the status of the tomcat application. 

    **Note:** The system shows that the tomcat application runs properly, which indicates that you have used the tomcat private image through the secret.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23476/154095167213587_en-US.png)



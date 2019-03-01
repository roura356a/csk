# Deploy Jenkins in a Kubernetes cluster and perform a pipeline build {#concept_avv_4b1_qgb .concept}

This topic describes how to deploy Jenkins, a continuous integration environment, in an Alibaba Cloud Kubernetes cluster, and how to perform an application pipeline build. The example in this topic details the pipeline build, including how to compile the source code of the application, build and push the application image, and deploy the application.

## Prerequisites {#section_ild_vyd_qgb .section}

You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

## Deploy Jenkins {#section_w1t_l12_qgb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Container Service-Kubernetes** \> **Store** \> **App Catalog**. Then, click **jenkins**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474238448_en-US.png)

3.  Click the **Values** tab.
4.  Modify the `AdminPassword` field to set a password.

    **Note:** To ensure that your password takes effect, you must remove the pound sign \(`#`\) before `AdminPassword`

    If you do not set any password, the system generates a password after Jenkins is deployed. You can run the following command to view the password:

    ```
    $ printf $(kubectl get secret --namespace ci jenkins-ci-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
    ```

5.  Select the target **Cluster** and **Namespace**, enter a **Release Name**, then click **DEPLOY**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474238449_en-US.png)

    **Note:** We recommend that you select a custom namespace or the default namespace. In this example, a custome namespace named test is selected.

6.  In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Service**.
7.  Select the cluster and the namespace used for deploying Jenkins.
8.  Click the external endpoint of the Jenkins service to log on to Jenkins.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474238450_en-US.png)


## Create a cluster certificate and an image repository certificate, and build and deploy an application {#section_zjq_gg2_qgb .section}

1.  Create a Kubernetes cluster certificate.
    1.  In the left-side navigation pane, click **Manage Jenkins**.
    2.  On the Manage Jenkins page, click **Configure System**.
    3.  In the **Cloud** area, click **Add** on the right of **Credentials**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474238458_en-US.png)

        Before adding a credential, you must obtain **KubeConfig** on the Basic Information tab page of the target Kubernetes cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338453_en-US.png)

    4.  In the diaplayed dialog box, set the following parameters:

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338526_en-US.png)

        -   **Kind**: Select **Docker Host Certificate Authentication**.
        -   **Client Key**: Paste the copied client-key-data content in KubeConfig.
        -   **Client Certificate**: Paste the copied client-certificate-data content in KubeConfig.
        -   **ID**: Enter the certificate ID. In this example, k8sCertAuth is entered.
        -   **Description**: Enter description content.
    5.  Click **Add**.
    6.  Test connectivity.

        Select the added credential in the preceding step from the **Credentials** drop-down list, and then click **Test Connection**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338459_en-US.png)

    7.  Set the Kubernetes cluster to dynamically build pods as follows.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338460_en-US.png)

    8.  Set the Kubernetes pod template.

        The slave-pipeline uses four containers to create each corresponding stage of the pipeline.

        -   Set container jnlp as follows.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338461_en-US.png)

            Use jenkins-slave-jnlp as a Docker image. The jenkins-slave-jnlp image is used to create the jnlp node to connect the Jenkins master.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-jnlp:3.14-1
            ```

        -   Set container kaniko as follows.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338462_en-US.png)

            Use jenkins-slave-kaniko as a Docker image. The jenkins-slave-kaniko image is used to create and push an image.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-kaniko:0.6.0
            ```

        -   Set container kubectl as follows.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338463_en-US.png)

            Use jenkins-slave-kubectl as a Docker image. The jenkins-slave-kubectl image is used to deploy the application.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-kubectl:1.11.5
            ```

        -   Set container maven as follows.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338465_en-US.png)

            Use jenkins-slave-maven as a Docker image.The jenkins-slave-maven image is used by mvn to package and build the application.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-maven:3.3.9-jdk-8-alpine
            ```

    9.  Set image repository permission for kaniko as follows.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338466_en-US.png)

    10. Click **Save**.
2.  To set image repository permission, use kubectl to create jenkins-docker-cfg secret in the target Kubernetes cluster.

    In this example, the Beijing image repository provided by Alibaba Cloud is used.

    ```
    $ docker login -u xxx -p xxx registry.cn-beijing.aliyuncs.com
    Login Succeeded
    $ kubectl create secret generic jenkins-docker-cfg -n ci --from-file=/root/.docker/config.json
    ```

3.  Create demo-pipeline and access the application service.
    1.  On the Jenkins home page, click **demo-pipeline**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338467_en-US.png)

    2.  In the left-side navigation pane, click **Build with Parameters**.
    3.  Modify the parameters according to your image repository information. In this example, the source code repository is master, and the image is registry.cn-beijing.aliyuncs.com/haoshuwei:stable.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474338468_en-US.png)

    4.  Click **Build**.
    5.  Click **Build History** to check the result. The following figure indicates a success.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122864/155143474438469_en-US.png)

    6.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs) to view the IP addresses of the services provided by the application.

Click [Here](https://github.com/AliyunContainerService/jenkins-demo.git) to obtain the source code repository used in the example.

For more information, see [Container Service](https://www.alibabacloud.com/zh/product/container-service).

For more information, see [kaniko](https://github.com/GoogleContainerTools/kaniko).


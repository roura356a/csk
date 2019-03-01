# Deploy Jenkins in a serverless Kubernetes cluster and perform an application pipeline build {#concept_fmw_tjf_qgb .concept}

This topic describes how to deploy Jenkins, a continuous integration environment, in an Alibaba Cloud serverless Kubernetes cluster, and how to perform an application pipeline build. The example in this topic details the pipeline build, including how to compile the source code of the application, build and push the application image, and deploy the application.

## Prerequisites {#section_byn_glf_qgb .section}

You have created a serverless Kubernetes cluster. For more information, see [Create a serverless Kubernetes cluster](../../../../../reseller.en-US/User Guide/Serverless Kubernetes cluster/Cluster management/Create a Serverless Kubernetes cluster.md#).

## Deploy Jenkins {#section_z1g_mlf_qgb .section}

1.  Run the following command to download the Jenkins deployment file:

    ```
    $ git clone https://github.com/AliyunContainerService/jenkins-on-serverless.git
    $ cd jenkins-on-serverless
    ```

2.  Persist the jenkins\_home directory.

    Serverless Kubernetes clusters do not support cloud disks. To persist the jenkins\_home directory, create the nfs volume, then modify the serverless-k8s-jenkins-deploy.yaml file to add the following field annotations and set nfs parameters:

    ```
    #volumeMounts:
            #  - mountPath: /var/jenkins_home
            #    name: jenkins-home
          .....
          #volumes:
          #  - name: jenkins-home
          #    nfs:
          #      path: /
          #      server:
    ```

3.  Run the following command to deploy Jenkins:

    ```
    $ kubectl apply -f serverless-k8s-jenkins-deploy.yaml
    ```

4.  Log on to Jenkins.
    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane, choose **Discovery and Load Balancing** \> **Service**.
    3.  Click the external endpoint of the Jenkins service to log on to Jenkins.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155143416538515_en-US.png)

    4.  On the Jenkins logon page, enter the user name and the password. The default user name and password are admin. We recommend that you modify them after you log on to Jenkins.

        ![](images/38516_en-US.png)


## Create a cluster certificate and an image repository certificate, and build and deploy an application {#section_xbb_t1g_qgb .section}

1.  Set Kubernetes Cloud parameters to dynamically create a slave pod.
    1.  In the left-side navigation pane, click **Manage Jenkins**.
    2.  Click **Configure System**.
    3.  In the **Cloud** area, enter the API server URL in KubeConfig as the **Kubernetes URL**.

        ![](images/38521_en-US.png)

    4.  Click **Add** on the right of **Credentials**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155143416538523_en-US.png)

        Before adding a credential, you must obtain **KubeConfig** on the Basic Information tab page of the target Kubernetes cluster.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155143416538524_en-US.png)

        In the displayed dialog box, set the following parameters:

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155143416538527_en-US.png)

        -   **Kind**: Select **Docker Host Certificate Authentication**.
        -   **Client Key**: Paste the copied client-key-data content in KubeConfig.
        -   **Client Certificate**: Paste the copied client-certificate-data content in KubeConfig.
        -   **ID**: Enter the certificate ID. In this example, k8sCertAuth is entered.
        -   **Description**: Enter description content.
    5.  Click **Add**.
    6.  Test connectivity.

        Select the added credential in the preceding step from the **Credentials** drop-down list, and then click **Test Connection**.

        ![](images/38529_en-US.png)

    7.  Enter the external endpoint of the jenkins service as **Jenkins URL**, and enter the external endpoint of the jenkins-agent service as **Jenkins tunnel**.

        ![](images/38531_en-US.png)

    8.  Click **Save**.
2.  To set image repository permission, use kubectl to create jenkins-docker-cfg secret in the target serverless Kubernetes cluster.

    In this example, the Beijing image repository provided by Alibaba Cloud is used.

    ```
    $ docker login -u xxx -p xxx registry.cn-beijing.aliyuncs.com
    Login Succeeded
    $ kubectl create secret generic jenkins-docker-cfg  --from-file=/root/.docker/config.json
    ```

3.  Create demo-pipeline and access the application service.
    1.  On the Jenkins home page, click **demo-pipeline**.
    2.  In the left-side navigation pane, click **Build with Parameters**.
    3.  Modify the parameters according to your image repository information. In this example, the source code repository branch is serverless, and the image is registry.cn-beijing.aliyuncs.com/haoshuwei:stable.

        ![](images/38622_en-US.png)

    4.  Click **Build**.
    5.  Click **Build History** to check the result. The following figure indicates a success.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/122940/155143416538532_en-US.png)

    6.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs) to view the IP addresses of the services provided by the application.

The source code repository used in this topic can be found at [https://github.com/AliyunContainerService/jenkins-demo](https://github.com/AliyunContainerService/jenkins-demo).


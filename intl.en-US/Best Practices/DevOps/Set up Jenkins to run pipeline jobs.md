# Set up Jenkins to run pipeline jobs

This topic describes how to build a continuous integration environment by using Jenkins in a Container Service for Kubernetes cluster. This topic also provides examples on how to build an application delivery pipeline that consists of source code compilation, image building and pushing, and application deployment.

## Prerequisites

[Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md)

## Deploy Jenkins

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, select **ack-jenkins**.

4.  Click the **Parameters** tab.

5.  Modify `AdminPassword`.

    **Note:** To ensure that the password takes effect, delete the number sign \(`#`\) before `AdminPassword`.

    If you do not modify `AdminPassword` to set a password, the system automatically generates a password after Jenkins is deployed. You can run the following command to query the password:

    ```
    printf $(kubectl get secret --namespace ci jenkins-ci-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
    ```

6.  ************In the Deploy section, select a Kubernetes cluster and a namespace, enter a release name, and then click **Create**.

    ![Namespace](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0346858951/p38449.png)

    **Note:** We recommend that you select a custom namespace or the default namespace. A custom namespace named ci is used in this example.

7.  In the left-side navigation pane, click **Clusters**.

8.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

9.  In the left-side navigation pane, click **Services**.

10. Select the cluster and namespace where Jenkins is deployed. Click the public endpoint of Jenkins to log on to Jenkins.


## Create a cluster certificate and an image repository certificate, and build and deploy an application

1.  Create a Kubernetes cluster certificate.

    1.  In the left-side navigation pane of the Jenkins console, click **Manage Jenkins**.

    2.  On the Manage Jenkins page, click **Configure System**.

    3.  In the **Cloud** section, find **Credentials**, and choose **Add** \> **Jenkins**.

        ![Credentials](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0346858951/p38458.png)

        Before you add a credential, go to the Connection Information tab of the Cluster Information page of the cluster in the ACK console, and check the **KubeConfig******. For more information, see [Configure a cluster credential](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

        ![Configure a cluster credential](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0346858951/p38453.png)

    4.  In the Add Credentials dialog box, set the following parameters:

        ![Add a credential](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1346858951/p38526.png)

        -   **Kind**: Select **Docker Host Certificate Authentication**.
        -   **Client Key**: Paste the value of client-key-data from KubeConfig.
        -   **Client Certificate**: Paste the value of client-certificate-data from KubeConfig.
        -   **ID**: Enter the certificate ID. In this example, the certificate ID is k8sCertAuth.
        -   **Description**: Enter a description.
    5.  Click **Add**.

    6.  Test connectivity.

        In the **Credentials** drop-down list, select the new credential and click **Test Connection**.

    7.  The Kubernetes cluster dynamically schedules build pods. The following figure shows the configurations of a build pod.

        ![Build pods](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1346858951/p38460.png)

    8.  Configure a Kubernetes pod template.

        The slave-pipeline template uses four containers to build stages in the pipeline.

        -   The following figure shows the configurations of the jnlp container.

            ![Container jnlp](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1346858951/p38461.png)

            Use the jenkins-slave-jnlp image as the Docker image. This image can be used to connect the Jenkins slave node and the master node through Java Network Launching Protocol \(JNLP\).

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-jnlp:3.14-1
            ```

        -   The following figure shows the configurations of the kaniko container.

            ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1346858951/p38462.png)

            Use the jenkins-slave-kaniko image as the Docker image. This image can be used to build and push application images.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-kaniko:0.6.0
            ```

        -   The following figure shows the configurations of the kubectl container.

            ![Container kubectl](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1346858951/p38463.png)

            Use the jenkins-slave-kubectl image as the Docker image. This image can be used to deploy applications in the kubectl container.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-kubectl:1.11.5
            ```

        -   The following figure shows the configurations of the maven container.

            ![Container maven](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1346858951/p38465.png)

            Use the jenkins-slave-maven image as the Docker image. This image allows you to package the build by using maven.

            ```
            registry.cn-beijing.aliyuncs.com/acs-sample/jenkins-slave-maven:3.3.9-jdk-8-alpine
            ```

    9.  The kaniko container requires the permission to access the image repository. The following figure shows the configurations.

        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1346858951/p38466.png)

    10. Click **Save**.

2.  To grant the permission to access the image repository, use kubectl to create a jenkins-docker-cfg secret in the Kubernetes cluster.

    In this example, an image repository provided by Container Registry in the China \(Beijing\) region is used.

    ```
    docker login -u xxx -p xxx registry.cn-beijing.aliyuncs.com
    ```

    ```
    kubectl create secret generic jenkins-docker-cfg -n ci --from-file=/root/.docker/config.json
    ```

3.  Build demo-pipeline and access the application.

    1.  On the homepage of the Jenkins console, click **demo-pipeline**.

        ![demo-pipeline](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2346858951/p38467.png)

    2.  In the left-side navigation pane, click **Build with Parameters**.

    3.  Modify the build parameters based on your image repository information. In this example, set branch to master, and use the stable image in the registry.cn-beijing.aliyuncs.com/haoshuwei repository.

        ![Build parameters](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2346858951/p38468.png)

    4.  Click **Build**.

    5.  Click Build History to check the result. The following figure indicates that the build was successful.

        ![Build History](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2346858951/p38469.png)


-   [Source code repository](https://github.com/AliyunContainerService/jenkins-demo.git)
-   [Container Service for Kubernetes \(ACK\)](https://www.alibabacloud.com/product/kubernetes)
-   [kaniko](https://github.com/GoogleContainerTools/kaniko)

  



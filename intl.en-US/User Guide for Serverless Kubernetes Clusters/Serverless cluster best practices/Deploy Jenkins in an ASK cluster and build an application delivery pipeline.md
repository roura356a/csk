---
keyword: [build an application delivery pipeline, deploy Jenkins]
---

# Deploy Jenkins in an ASK cluster and build an application delivery pipeline

This topic describes how to deploy Jenkins, which contains a continuous integration environment, in a serverless Kubernetes \(ASK\) cluster. This topic also provides step-by-step instructions on how to build an application delivery pipeline that includes source code compilation, image building and pushing, and application deployment.

Before you start, make sure that the following requirements are met:

-   [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md)

## Deploy Jenkins

1.  Run the following command to download the Jenkins package:

    ```
    git clone https://github.com/AliyunContainerService/jenkins-on-serverless.git
    cd jenkins-on-serverless
    ```

2.  Configure persistent storage for the `jenkins_home` directory.

    ASK clusters do not support Alibaba Cloud disks. If you require persistent storage, you can mount an `nfs volume` to the `jenkins_home` directory. You must modify the serverless-k8s-jenkins-deploy.yaml file to remove the following annotations and set nfs parameters:

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
    kubectl apply -f serverless-k8s-jenkins-deploy.yaml
    ```

4.  Log on to Jenkins.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Applications** in the **Actions** column.

    4.  In the left-side navigation pane, click **Services**.

    5.  Select Jenkins and click its external endpoint to log on.

        ![External endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7742221261/p141997.png)

    6.  On the Jenkins logon page, enter the username and password. The default username and password are both **admin**. We recommend that you change the username and password after you log on.

        ![Jenkins logon page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8397297951/p38516.png)


## Create a cluster certificate and an image repository certificate, and create and deploy an application

1.  Set Kubernetes Cloud parameters to dynamically create a slave pod.

    1.  In the left-side navigation pane, click **Manage Jenkins**.

    2.  In the Manage Jenkins panel, click **Configure System**.

    3.  In the **Cloud** section, set **Kubernetes URL** to the API server URL in KubeConfig.

        ![Kubernetes URL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8397297951/p38521.png)

    4.  Click **Add** on the right side of **Credentials**.

        ![Add](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9397297951/p38523.png)

        Before you add a credential, go to the Basic Information page of the ASK cluster and find **KubeConfig** below **Configure the cluster credentials**.

        ![KubeConfig](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9397297951/p38524.png)

        In the Add Credentials dialog box, set the following parameters:

        ![Add a credential](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9397297951/p38527.png)

        -   **Kind**: Select **Docker Host Certificate Authentication**.
        -   **Client Key**: Enter the value of client-key-data obtained from KubeConfig.
        -   **Client Certificate**: Paste the value of client-certificate-data from KubeConfig.
        -   **ID**: Enter the certificate ID. k8sCertAuth is entered in this example.
        -   **Description**: Enter a description.
    5.  Click **Add**.

    6.  Test the connectivity.

        From the **Credentials** drop-down list, select the newly added credential and click **Test Connection**.

        ![3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6588654061/p142012.png)

    7.  Set **Jenkins URL** to the external endpoint of the Jenkins service and set **Jenkins tunnel** to the external endpoint of the jenkins-agent service.

        ![Jenkins URL](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9397297951/p38531.png)

    8.  Click **Save**.

2.  To set permissions on the image repository, use kubectl to create a jenkins-docker-cfg Secret in the ASK cluster.

    An image repository provided by Alibaba Cloud in the China \(Beijing\) region is used in this example:

    ```
    docker login -u xxx -p xxx registry.cn-beijing.aliyuncs.com
    #Output:
    Login Succeeded
    
    kubectl create secret generic jenkins-docker-cfg  --from-file=/root/.docker/config.json
    ```

3.  Build demo-pipeline and access the application.

    1.  On the Jenkins homepage, click **demo-pipeline**.

    2.  In the left-side navigation pane, click **Build with Parameters**.

    3.  Modify the parameters based on the image repository information. Set **api\_server\_url** to the API server URL in KubeConfig. The source code repository branch is serverless and the image is **registry.cn-beijing.aliyuncs.com/haoshuwei/jenkins-java-demo:serverless** in this example.

        ![api_server_url](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9397297951/p38622.png)

    4.  Click **Build**.

    5.  Click Build History to check the result. The following figure indicates that the build is successful.

        ![Build History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9397297951/p38532.png)

    6.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com) to view the IP addresses of the services provided by the application.


## Source code repository

Click [Code repository](https://github.com/AliyunContainerService/jenkins-demo.git) to obtain the source code used in this topic.


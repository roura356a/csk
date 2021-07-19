---
keyword: [build an application delivery pipeline, deploy Jenkins]
---

# Deploy Jenkins in an ASK cluster and build an application delivery pipeline

This topic describes how to deploy a Jenkins continuous integration environment in a serverless Kubernetes \(ASK\) cluster. This topic also provides step-by-step instructions on how to build an application delivery pipeline that includes source code compilation, image building and pushing, and application deployment.

-   An ASK cluster is created. For more information, see [Create an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Quick start/Create an ASK cluster.md).
-   A kubectl client is connected to the cluster. For more information, see [Use kubectl to connect to an ASK cluster](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Cluster/Manage and access clusters/Use kubectl to connect to an ASK cluster.md).

## Deploy Jenkins

1.  Run the following command to download the Jenkins package:

    ```
    git clone https://github.com/AliyunContainerService/jenkins-on-serverless.git
    cd jenkins-on-serverless
    ```

2.  Store the `jenkins_home` directory in persistent storage.

    ASK clusters do not support Alibaba Cloud disks. If you require persistent storage, you can mount an `nfs volume` to the `jenkins_home` directory. You must modify the serverless-k8s-jenkins-deploy.yaml file to uncomment the following lines and set Network File System \(NFS\) parameters:

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

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  Select Jenkins and click its external endpoint to log on to Jenkins.

        ![External endpoint](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7742221261/p141997.png)

    5.  On the Jenkins logon page, enter the username and password. The default username and password are both **admin**.

        **Note:** To ensure system security, change the password after you log on to Jenkins.

5.  Configure the settings on the Getting Started page.

    1.  On the Getting Started page, click **Install suggested plugins**.

    2.  After the plug-ins are installed, click **Save and Finish** in the **Instance Configuration** section of the Getting Started page.

    3.  Click **Start using Jenkins**.

6.  Obtain the Secret that contains a token.

    1.  Run the following command to query the token:

        ```
        kubectl get secret
        ```

        Expected output:

        ```
        NAME                         TYPE                                  DATA   AGE
        ack-jenkins-sa-token-q****   kubernetes.io/service-account-token   3      28m
        default-token-b****          kubernetes.io/service-account-token   3      27h
        ```

    2.  Run the following command to obtain the Secret named **ack-jenkins-sa-token-q\*\*\*\***:

        ```
        kubectl get secret ack-jenkins-sa-token-q**** -o jsonpath={.data.token} |base64 -d
        ```

        Expected output:

        ```
        sdgdrh****
        ```

7.  Create a credential of the Secret text type.

    1.  In the left-side navigation pane of the Jenkins dashboard, click **Manage Jenkins**.

    2.  On the Manage Jenkins page, click **Manage Nodes and Clouds** in the **System Configuration** section.

    3.  In the left-side navigation pane of the Nodes page, click **Configure Clouds**.

    4.  On the Configure Clouds page, click **Kubernetes Cloud details...**.

    5.  In the **Credentials** field, choose **Add** \> **Jenkins**.

    6.  In the Jenkins Credentials Provider: Jenkins dialog box, add a credential of the Secret text type.

        The following table describes the credential parameters.

        |Parameter|Description|
        |---------|-----------|
        |Domain|The domain of the credential. Default value: **Global credentials \(unrestricted\)**.|
        |Kind|The kind of the credential. In this example, **Secret text** is selected.|
        |Scope|The scope of the credential. Valid values: Global and System. In this example, **Global \(Jenkins, nodes, items, all child items, etc\)** is selected.|
        |Secret|The Secret of the credential. In this example, the Secret obtained from the previous step is entered.|
        |ID|The name of the credential. In this example, **ask-jenkins-token** is entered.|
        |Description|The description of the credential.|

    7.  Click **Add**.

8.  On the Configure Clouds page, set the required parameters. For more information, see [Configure Kubernetes Cloud](/intl.en-US/Best Practices/DevOps/Set up Jenkins to run pipeline jobs.md).

    1.  Set the **Kubernetes address**, set **Credentials** to **ask-jenkins-token**, and then click **Connection test** to check the connectivity between Jenkins and the cluster.

    2.  Set the **Jenkins address** and **Jenkins channel** parameters.

    3.  Click **Save**.

9.  Build **demo-pipeline** and access the application.

    1.  On the Jenkins homepage, click the ![Build](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4133866261/p276903.png) icon to the right of **demo-pipeline**.

        ![demo-pipeline](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4391491261/p38467.png)

    2.  Modify the build parameters based on the information of your image repository. In this example, the branch of the source code repository is `master` and the image is `registry.cn-beijing.aliyuncs.com/ack-cicd/ack-jenkins-demo:latest`.

        ![Build parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4391491261/p38468.png)

    3.  Click **Build**.

        Test the connectivity between the Jenkins master and the Jenkins slave pod that is dynamically allocated by the Kubernetes cluster.

        After you click Build, Jenkins dynamically creates a slave pod from the Kubernetes cluster to run the build job. For more information about the sample application code, see [jenkins-demo-GitHub](https://github.com/AliyunContainerService/jenkins-demo) or [jenkins-demo-haoshuwei](https://code.aliyun.com/haoshuwei/jenkins-demo.git).

    4.  Go to the **Status** page. If the build is successful, it indicates that Jenkins runs as normal on Kubernetes.

        ![Build History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4391491261/p38469.png)


-   For more information about how to configure the Maven cache for slave pods, see [Configure the Maven cache for slave pods](/intl.en-US/Best Practices/DevOps/Set up Jenkins to run pipeline jobs.md).
-   For more information about how to use kaniko to build and push container images, see [Use kaniko to build and push container images](/intl.en-US/Best Practices/DevOps/Set up Jenkins to run pipeline jobs.md).


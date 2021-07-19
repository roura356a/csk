---
keyword: [set up Jenkins, pipeline jobs]
---

# Set up Jenkins to run pipeline jobs

This topic describes how to set up a Jenkins continuous integration environment in a Container Service for Kubernetes \(ACK\) cluster. This topic also provides examples on how to build an application delivery pipeline that covers source code compilation, image building and pushing, and application deployment.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   A kubectl client is connected to the cluster. For more information, see [Connect to Kubernetes clusters by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

## Deploy Jenkins

We recommend that you perform the following steps to install ack-jenkins in the cluster and run a build job named **demo-pipeline**. After the build job is complete, you can customize the configuration of the build job based on your requirements.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **Alibaba Cloud Apps** tab, select **ack-jenkins**.

4.  On the App Catalog - ack-jenkins page, set the required parameters.

    1.  Click the **Parameters** tab.

    2.  In the Deploy section, select your cluster from the **Cluster** drop-down list and a namespace from the **Namespace** drop-down list. Then, enter a release name in the **Release Name** field.

        ![Namespace](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0346858951/p38449.png)

        **Note:** We recommend that you select a custom namespace or the **default** namespace. In this example, a custom namespace named **ci** is selected.

    3.  On the Parameters tab, modify the value in the `AdminPassword` field. Then, click **Create** in the Deploy section.

        If you do not enter a password into the `AdminPassword` field, the system automatically generates a password after Jenkins is deployed. The default password is `admin`.

5.  Log on to Jenkins.

    1.  In the left-side navigation pane of the ACK console, click **Clusters**.

    2.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    3.  Select the namespace in which Jenkins is deployed. Find the **ack-jenkins-default** Service and then click the endpoint in the **External Endpoint** column to log on to Jenkins.

6.  Configure the settings on the Getting Started page.

    1.  On the Getting Started page, click **Install suggested plugins**.

    2.  After the plug-ins are installed, click **Save and Finish** in the **Instance Configuration** section of the Getting Started page.

    3.  After the instance configuration is saved, click **Start using Jenkins**.

7.  Build **demo-pipeline** and access the application.

    1.  On the Jenkins homepage, click the ![Build](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4133866261/p276903.png) icon to the right of **demo-pipeline**.

        ![demo-pipeline](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4391491261/p38467.png)

    2.  Modify the build parameters based on the information of your image repository. In this example, the branch of the source code repository is `master` and the image is `registry.cn-beijing.aliyuncs.com/ack-cicd/ack-jenkins-demo:latest`.

        ![Build parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4391491261/p38468.png)

    3.  Click **Build**.

        Test the connectivity between the Jenkins master and the Jenkins slave pod that is dynamically allocated by the Kubernetes cluster.

        After you click Build, Jenkins dynamically creates a slave pod from the Kubernetes cluster to run the build job. For more information about the sample application code, see [jenkins-demo-GitHub](https://github.com/AliyunContainerService/jenkins-demo) or [jenkins-demo-haoshuwei](https://code.aliyun.com/haoshuwei/jenkins-demo.git).

    4.  Go to the **Status** page. If the build is successful, it indicates that Jenkins runs as normal on Kubernetes.

        ![Build History](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4391491261/p38469.png)


## Configure Kubernetes Cloud

Jenkins uses the Kubernetes plug-in to connect to Kubernetes clusters, and then dynamically creates and releases slave pods.

1.  In the left-side navigation pane, click **Back to Dashboard**.
2.  In the left-side navigation pane of the Jenkins dashboard, click **Manage Jenkins**.
3.  On the Manage Jenkins page, click **Manage Nodes and Clouds** in the **System Configuration** section.
4.  In the left-side navigation pane of the Nodes page, click **Configure Clouds**.
5.  On the Configure Clouds page, click **Kubernetes Cloud details...**.

    Kubernetes Cloud is automatically configured when ack-jenkins is deployed. The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Name|Default value: `kubernetes`. ![Build pods](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1315514161/p38460.png) |
    |Kubernetes address|Default value: `https://kubernetes.default.svc.cluster.local:443`. This indicates that Jenkins is installed in the cluster and can access the Kubernetes API server by using an internal endpoint.|
    |Kubernetes namespace|Default value: `default`. This indicates that slave pods are dynamically created and destroyed in the `default` namespace.|
    |Jenkins address|The endpoint that the slave pods use to connect to the Jenkins master. Default value: `http://ack-jenkins-default:8080`.|
    |Jenkins channel|The Java network launch protocol \(JNLP\) that the slave pods use to connect to the Jenkins master. Default value: `ack-jenkins-default-agent:50000`.|

6.  On the Configure Clouds page, click **Pod Templates...** and then click **Pod Templates details...**.

    A pod template is automatically configured when ack-jenkins is deployed. The following table describes the parameters.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the slave pod. Default value: `slave-pipeline`. ![Container jnlp](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38461.png) |
    |Namespace|Default value: `jenkins`.|
    |The list of labels|Build jobs select pod templates with matching labels to create slave pods. Default value: `slave-pipeline`.|
    |The list of containers|`jnlp` is used to connect to the Jenkins master.|
    |`kaniko` is used to build and push container images. ![Container kaniko](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38462.png) |
    |`maven` is used to build and package applications. ![Container maven](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38465.png) |
    |`kubectl` is used to deploy applications. ![Container kubectl](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1346858951/p38463.png) |


## Configure the Maven cache for slave pods

Slave pods are dynamically created in the Kubernetes cluster and may be scheduled to a random worker node. To ensure that each dynamically created slave pod can use the **Maven cache**, a shared persistent volume is required.

1.  Create Apsara File Storage NAS \(NAS\) volumes to provide shared persistent storage. For more information, see [Use a dynamically provisioned NAS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Use a dynamically provisioned NAS volume.md).

    Run the following command to query the NAS volumes that are created in the **jenkins** namespace:

    ```
    kubectl -n jenkins get pvc
    ```

    Expected output:

    In the expected output, the persistent volume claim \(PVC\) named `ack-jenkins-default` provides persistent storage for the /var/jenkins\_home directory of the Jenkins master. The PVC named `nas-csi-pvc` provides shared persistent storage for the Maven cache.

    ```
    NAME                  STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS               AGE
    ack-jenkins-default   Bound    d-2ze8xhgzuw1t278j****                     20Gi       RWO            alicloud-disk-efficiency   3h16m
    nas-csi-pvc           Bound    nas-c6c3e703-58a9-484e-8ce5-630486ea****   20Gi       RWX            alicloud-nas-subpath       4m17s
    ```

2.  Add a ConfigMap volume in the pod template.

    To mount a custom settings file to a slave pod in Jenkins, you can create a ConfigMap volume and then configure the volume in the pod template.

    1.  Run the following command to create a custom settings file:

        ```
        kubectl -n jenkins create configmap maven-config --from-file=settings.xml 
        ```

    2.  In the left-side navigation pane of the Jenkins dashboard, click **Manage Jenkins**. On the Manage Jenkins page, click **Manage Nodes and Clouds** in the **System Configuration** section. In the left-side navigation pane of the Nodes page, click **Configure Clouds**.

    3.  On the Configure Clouds page, click **Pod Template** and then click **Pod Template details**.

    4.  In the **Volumes** field, click **Add Volume** and then select **Config Map Volume**.

        ![Add volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4133866261/p281884.png)

    5.  Click **Save**.

3.  Add a PVC in the pod template. For more information, see [Step 2](#step_ym0_8j1_oj4).

    ![Add a PVC](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4133866261/p281899.png)

    **Verify the result**

    1.  On the Jenkins homepage, click **demo-pipeline**.
    2.  In the left-side navigation pane of the Jenkins dashboard, click **Configure**.
    3.  Click the Pipeline tab.
    4.  In the **Script Path** field, enter Jekinsfile.maven.
    5.  Click **Save**.
    6.  On the Jenkins homepage, click the ![Build](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4133866261/p276903.png) icon to the right of **demo-pipeline**. Then, click **Build**.

        When you build the **Maven** project for the first time, the system requires some time to download all dependent packages.

        ![First build](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5133866261/p278605.png)

        When you build the **Maven** project again, the system can reference dependent packages from the cache and quickly package the source code.

        ![Build again](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5133866261/p278624.png)


## Use kaniko to build and push container images

When you use **kaniko** to push images, you must configure credentials that are used to access the image repository.

In this example, you must generate a config.json file in a Linux machine. This file is used to access the image repository. You must not generate the file in a macOS machine. The following example shows how to build and push the `registry.cn-hangzhou.aliyuncs.com/haoshuwei24/jenkins-demo:20200428` image.

1.  Run the following command to log on to the image repository:

    The config.json file is automatically generated when you log on to the image repository.

    ```
    docker login -u <username> -p <password> registry.cn-hangzhou.aliyuncs.com
    ```

    Expected output:

    ```
    Login Succeeded
    ```

2.  Create a Secret named jenkins-docker-cfg in the **jenkins** namespace from the config.json file.

    ```
    kubectl create secret generic jenkins-docker-cfg -n jenkins --from-file=/root/.docker/config.json
    ```

3.  Add an environment variable and a Secret volume in the pod template.

    1.  In the left-side navigation pane of the Jenkins dashboard, click **Manage Jenkins**. On the Manage Jenkins page, click **Manage Nodes and Clouds** in the **System Configuration** section. In the left-side navigation pane of the Nodes page, click **Configure Clouds**.

    2.  On the Configure Clouds page, click **Pod Template** and then click **Pod Template details**.

    3.  In the **EnvVars** field, click **Add Environment Variable** and then select **Environment Variable**.

        ![Add an environment variable and a Secret volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0906766261/p280887.png)

    4.  In the **Volumes** field, click **Add Volume** and then select **Secret Volume**.

    5.  Click **Save**.

    **Verify the result**

    1.  On the Jenkins homepage, click **demo-pipeline**.
    2.  In the left-side navigation pane of the Jenkins dashboard, click **Configure**.
    3.  Click the Pipeline tab.
    4.  In the **Script Path** field, enter Jekinsfile.kaniko.
    5.  Click **Save**.
    6.  On the Jenkins homepage, click the ![Build](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4133866261/p276903.png) icon to the right of **demo-pipeline**. Modify the build parameters based on the information of your image repository. Then, click **Build**.

        View the **kaniko** build log.

        ![kaniko build log](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5133866261/p280900.png)

    7.  Check whether the image is successfully pushed to the image repository.
        1.  Log on to the [Container Registry console](https://cr.console.aliyun.com).
        2.  In the left-side navigation pane, click **Instances**.
        3.  On the management page of the Container Registry Personal Edition instance, choose **Repositories** \> **Repositories** in the left-side navigation pane.
        4.  On the **Repositories** page, find the repository that you want to manage and click **Manage** in the Actions column.
        5.  In the left-side navigation pane, click **Tags** to check whether the image is successfully pushed to the repository.

            ![Check the image tag](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0906766261/p280924.png)


## Upgrade Jenkins

If you want to upgrade the jenkins-master image in your Jenkins environment, you must create a snapshot of the disk volume that is mounted on the /var/jenkins\_home directory. This prevents data loss because the automated script of the new Jenkins master automatically writes data to the /var/jenkins\_home directory. For more information, see [Use volume snapshots created from disks](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/Disk volumes/Use volume snapshots created from disks.md).

-   [Source code repository](https://github.com/AliyunContainerService/jenkins-demo.git)
-   [ACK](https://www.alibabacloud.com/product/kubernetes)
-   [kaniko](https://github.com/GoogleContainerTools/kaniko)

  



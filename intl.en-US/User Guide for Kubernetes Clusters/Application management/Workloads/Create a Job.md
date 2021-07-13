---
keyword: [Job, create a Job]
---

# Create a Job

You can use a Job to create an application in the Container Service for Kubernetes \(ACK\) console. This topic describes how to use a Job to create a busybox application and the operations that you can perform on the application.

An ACK cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

A Job processes multiple short-lived, one-off tasks at a time to ensure that one or more pods in the tasks terminate with success.

Kubernetes supports the following types of Jobs:

-   Non-parallel Jobs: In most cases, a Job of this type starts only one pod unless the pod fails. The Job is considered complete when the pod terminates with success.
-   Jobs with a fixed completion count: A non-zero positive value is specified for `.spec.completions`. A Job of this type starts pods one after one. The Job is considered complete when the number of pods that terminate with success is equal to the value of `.spec.completions`.
-   Parallel Jobs with a work queue: A non-zero positive value is specified for `.spec.Parallelism`. A Job of this type starts multiple pods at a time. The Job is considered complete when all pods terminate and at least one pod terminates with success. `.spec.completions` is not required.
-   Parallel Jobs with a fixed completion count: A Job of this type has both `.spec.completions` and `.spec.Parallelism` specified. The Job starts multiple pods at a time to process a work queue.

Jobs can manage pods based on the settings of `.spec.completions` and `.spec.Parallelism` as described in the following table.

**Note:** The Job created in this example is a parallel Job with a fixed completion count.

|Job type|Scenario|Action|completions|Parallelism|
|--------|--------|------|-----------|-----------|
|One-off Job|Migrate a database|The Job starts only one pod. The Job is complete when the pod terminates with success.|1|1|
|Job with a fixed completion count|Start pods one after one to process a work queue|The Job starts pods one after one. The Job is complete when the number of pods that terminate with success reaches the value of .spec.completions.|2+|1|
|Parallel Job with a fixed completion count|Start multiple pods at a time to process a work queue|The Job starts multiple pods at a time. The Job is complete when the number of pods that terminate with success reaches the value of .spec.completions.|2+|2+|
|Parallel Job|Start multiple pods at a time to process a work queue|The Job starts one or more pods at a time. The Job is complete when at least one pod terminates with success.|1|2+|

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Jobs**.

5.  On the **Jobs** page, click **Create from Image** in the upper-right corner.

    -   **Name**: Enter a name for the application.
    -   **Type**: Select **Jobs** from the drop-down list.

        **Note:** The application is created by using a **Job** in this example.

    -   **Label**: Add a label to the application. The label is used to identify the application.
    -   **Annotations**: Add an annotation to the application.
    -   **Synchronize Timezone**: Specify whether to synchronize the time zone between nodes and containers.
    ![Configuration](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8329301161/p13569.png)

6.  Configure containers.

    **Note:** You can configure one or more containers for the pods of the application.

    1.  Configure basic settings.

        -   **Image Name**: Select an image.
            -   To use a Docker image or an image from Container Registry, click **Select Image**. In the dialog box that appears, select an image and click **OK**. In this example, an NGINX image is selected. On the **Search** tab, you can select **Docker Images** from the drop-down list, enter NGINX in the search box, and then click Search.
                -   Alibaba Cloud Container Registry: On the **Alibaba Cloud Container Registry** tab, you can select an image from Container Registry. You must specify the region and Container Registry instance to which the image belongs. For more information about Container Registry, see [What is Container Registry Enterprise Edition?]().

                    **Note:** On the **Alibaba Cloud Container Registry** tab, you can search images by name in Container Registry.

                -   Docker Official Images: On the **Docker Official Images** tab, you can select a Docker image.
                -   Favorite Images: On the **Favorite Images** tab, you can select a Docker image that you have specified as one of your favorite images.
                -   Search: On the **Search** tab, you can select Alibaba Cloud Images from the drop-down list and specify a region to search for an image in Container Registry. You can also select Docker Images from the drop-down list and search for a Docker image.
            -   You can also enter the address of a private registry. The registry address must be in the following format: `domainname/namespace/imagename:tag`.
        -   **Image Version**:
            -   Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.
            -   You can select the following image pull policies:

                -   **ifNotPresent**: If the image that you want to pull is found on your on-premises machine, the image on your on-premises machine is used. Otherwise, ACK pulls the image from the corresponding repository.
                -   **Always**: ACK pulls the image from Container Registry each time the application is deployed or scaled out.
                -   **Never**: ACK uses only images on your on-premises machine.
                **Note:** If you select **Image Pull Policy**, no image pull policy is applied.

            -   To pull the image without a password, click **Set Image Pull Secret** to set a Secret that is used to pull the image. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use aliyun-acr-credential-helper to pull images without a password.md).
        -   **Always**: If you do not select this option, ACK caches the pulled image. This improves the efficiency deploying applications. If the specified image version is the same as the cached image version, ACK deploys the application from the cached image. Therefore, when you update the application code, if you do not change the image version for reasons such as to support the upper-layer workloads, the previously cached image is used. If you set Image Pull Policy to Always, ACK pulls the image from the repository each time the application is deployed. This ensures that the latest image and code are used.
        -   **Set Image Pull Secret**: If you use a private image, we recommend that you configure a Secret to secure the image. For more information, see [Create an application by using an image pull Secret](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Create a stateless application by using a Deployment.md).
        -   **Resource Limit**: You can specify an upper limit for the CPU and memory resources that the application can consume. This prevents the application from occupying an excessive amount of resources. CPU resources are measured in millicores. Each millicore is one thousandth of one core. Memory resources are measured in bytes, which can be GiB, MiB, or KiB.
        -   **Required Resources**: The amount of CPU and memory resources that are reserved for the application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other services or processes occupy these resources.
        -   **Container Start Parameter**: Select **stdin** to send the input from the ACK console to the container. Select **tty** to send start parameters that are defined in a virtual terminal to the console.
        -   **Privileged Container**: If you select Privileged Container, privileged=true is set for the container. This indicates that the privilege mode is enabled. If you do not select Privileged Container, privileged=false is set for the container. This indicates that the privilege mode is disabled.
        -   **Init Container**: If you select this check box, an init container is created. Init containers contain practical tools. For more information, see [Init Container](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/).
        -   **Port**: Set the host port and container port. TCP and UDP are supported.
        ![Basic settings of the container](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0145359951/p13606.png)

    2.  Set parameters in the **Environments** section.

        You can configure environment variables in key-value pairs for pods. Environment variables are used to apply pod configurations to containers. For more information, see [Pod variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    3.  Configure the lifecycle of the container.

        You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Configure the lifecycle of a container](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Start**: Set the command and parameter that take effect before the container starts.
        -   **Post Start**: Set the command that takes effect after the container starts.
        -   **Pre Stop**: Set the command that takes effect before the container stops.
        ![Lifecycle settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6705677161/p134220.png)

    4.  Configure volumes.

        Local storage and cloud storage are supported.

        -   **Local storage**: You can select HostPath, ConfigMap, Secret, and EmptyDir. The storage volume is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **Cloud storage**: supports disks, Apsara File Storage NAS \(NAS\) file systems, and Object Storage Service \(OSS\) buckets.
    5.  Configure **Log Service**. You can specify collection configurations and add custom tags.

        **Note:** Make sure that the Log Service agent is installed in the cluster.

        You can set the Collection Configuration parameter.

        -   **Logstore**: Create a Logstore in Log Service to store the collected log data.
        -   **Log Path in Container**: Specify stdout or a path to collect log data.
            -   **stdout**: specifies that the stdout files are collected.
            -   **Text Logs**: specifies that the log files in the specified path of the container are collected. Wildcard characters can be used in the path.
        You can also add custom tags. Custom tags are added to the log files of the container when the log files are collected. Log data with tags is easier to aggregate and filter.

7.  After you complete the configurations, click **Next**.

8.  Configure advanced settings.

    You can set parameters in the **Job Settings** section.

    |Parameter|Description|
    |---------|-----------|
    |**Completions**|The number of pods that the Job must run. Default value: 1.|
    |**Parallelism**|The number of pods that the Job must run in parallel at any time. Default value: 1.|
    |**Timeout**|The activeDeadlineSeconds field in the YAML file. This field specifies the operating time limit of the Job. If the Job is not complete within the time limit, the system attempts to terminate the Job.|
    |**BackoffLimit**|The backoffLimit field in the YAML file. This field specifies the number of retries that are performed by the Job upon failure. Each time the Job fails, the failed pods associated with the Job are restarted with time delay. The time delay exponentially increases until a maximum of six minutes. Default value: 6.|
    |**Restart**|Only Never and OnFailure restart policies are supported.|

    ![Advanced settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1145359951/p13639.png)

9.  Click **Create**.

10. After the application is created, you are redirected to the Complete wizard page. The resource objects of the application are displayed.

    ![Creation complete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1145359951/p13640.png)

    You can click **View Details** to view application details.

    You can view the status of pods in the **Status** column. In this example, two pods are created in parallel based on the configuration of the Job.

    Wait until all pods are created.

11. Click the **Back** icon in the upper-left corner of the page. On the Jobs page, you can view the time when the Job is complete.

    **Note:** If the pods associated with the Job are not complete, the end time is not displayed.


## What to do next

In the left-side navigation pane of the ACK console, click **Clusters** to go to the Clusters page. Find the cluster that you want to manage, and click the cluster name or click **Details** in the **Actions** column. On the details page of the cluster, choose **Workloads** and Jobs in the left-side navigation pane. On the **Jobs** page, find the application that you want to manage, and click the application name or click **Details** in the **Actions** column. On the details page of the application, you can **scale** and **refresh** the application. You can also **view the YAML file** of the application.

-   Scale: On the details page of the application, click **Scale** in the upper-right corner to scale the application to a required number of replicated pods.
-   View the YAML file: On the details page of the application, click **View in YAML** in the upper-right corner. You can **update** and **download** the YAML file. You can also **save** the YAML file as a template.
-   Refresh: On the details page of the application, click **Refresh** in the upper-right corner to update the application details.


# Create a Job application by using an image

Container Service for Kubernetes \(ACK\) allows you to create Job applications in the ACK console. This topic describes the features of Job applications and how to create a Job application. A Job application named busybox is created in the following example.

A Kubernetes cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

A Job processes multiple short-lived one-off tasks at a time to ensure that one or more pods in the tasks successfully terminate.

Kubernetes supports the following types of Jobs:

-   Non-parallel Job: In most cases, a Job of this type starts only one pod unless the pod fails. The Job is considered complete when the pod successfully terminates.
-   Jobs with a fixed completion count: A non-zero positive value is specified for the `.spec.completions` parameter. The Job starts pods one by one. The job is considered complete when the number of pods that successfully terminate is equal to the value of `.spec.completions`.
-   Parallel Jobs with a work queue: A non-zero positive value is specified for the `.spec.Parallelism` parameter. The Job starts multiple pods at a time. The Job is considered complete when all pods terminate and at least one pod successfully terminates.``
-   Parallel Job with a fixed completion count: A Job of this type has both `.spec.completions` and `.spec.Parallelism` specified. The Job starts multiple pods at a time to process a work queue.

Jobs can manage pods based on the settings of `.spec.completions` and `.spec.Parallelism` as described in the following table.

**Note:** The Job that is created in this example is a parallel Job with a fixed completion count.

|Job type|Usage example|Action|Completions|Parallelism|
|--------|-------------|------|-----------|-----------|
|One-off Job|Database migration|The Job starts only one pod. The Job is completed when the pod successfully terminates.|1|1|
|Job with a fixed completion count|Start pods one by one to process a work queue|The Job starts pods one by one. The Job is completed when the number of pods that successfully terminate reaches the value of .spec.completions.|2 or more|1|
|Parallel Job with a fixed completion count|Start multiple pods at a time to process a work queue|The Job starts multiple pods at a time. The Job is completed when the number of pods that successfully terminate reaches the value of .spec.completions.|2 or more|2 or more|
|Parallel Job|Start multiple pods at a time to process a work queue|The Job starts one or more pods at a time. The Job is completed when a pod successfully terminates.|1|2 or more|

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Workload** page, click the **Jobs** tab. Then, click Create from Image. On the Basic Information wizard page, set the following parameters:

    -   **Name**: the name of the application.
    -   **Cluster**: the cluster in which the application is deployed.
    -   **Namespace**: the namespace in which the application is deployed. If you do not set the namespace, the system uses the default namespace.
    -   **Type**: the application type. Select **Job** from the drop-down list.

        **Note:** A **Job** application is created in this example.

    -   **Label**: Add labels to the application.
    -   **Annotations**: Add annotations to the application.
    -   **Synchronize Timezone**: Specify whether the containers and the node use the same time zone.
    ![Configurations](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8329301161/p13569.png)

6.  Configure containers.

    **Note:** You can configure one or more containers for the pods of the application.

    1.  Set basic parameters.

        -   **Image Name**: Specify the image name.
            -   You can click **Select Image**. In the dialog box that appears, select an image and click **OK**. In this example, an NGINX image is selected. On the **Search** tab, you can select **Docker Images** from the drop-down list, enter NGINX in the search bar, and then click Search.
                -   Alibaba Cloud Container Registry: On the **Alibaba Cloud Container Registry** tab, you can select an image in Alibaba Cloud Container Registry \(ACR\). Specify the region and ACR instance of the image. For more information, see [What is Container Registry Enterprise Edition?]().

                    **Note:** On the **Alibaba Cloud Container Registry** tab, you can enter an image name to search for an image in ACR.

                -   Docker Official Images: On the **Docker Official Images** tab, you can select a Docker image.
                -   Favorite Images: On the **Favorite Images** tab, you can select a Docker image that you have specified as one of your favorite images.
                -   Search: On the **Search** tab, you can select Alibaba Cloud Image from the drop-down list and specify a region to search for an image in ACR. You can also select Docker Images from the drop-down list and search for the Docker images.
            -   You can also enter a private registry in the format of `domainname/namespace/imagename:tag`.
        -   **Image Version**:
            -   Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.
            -   You can select the following image pull policies:

                -   **ifNotPresent**: If the image you want to pull is found in the region where the cluster is deployed, the local image is used. Otherwise, ACK pulls the image from the corresponding repository.
                -   **Always**: ACK pulls the image from the repository each time the application is deployed or expanded.
                -   **Never**: ACK uses only local images.
                **Note:** If you select **Image Pull Policy**, no image pull policy is applied for the deployment of the application.

            -   To pull the image without a secret, click **Set Image Pull Secret** to set a secret for pulling images. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use aliyun-acr-credential-helper to pull images without a password.md).
        -   **Always**: If you do not select this option, ACK caches the pulled image. This improves the efficiency of pulling and deploying images. If the specified image version is the same as the cached image version, ACK deploys the application from the cached image. When you update the application code, you can retain the image version as required. For example, you can retain the image version to support the upper-layer workloads. In this case, ACK deploys the application from the previously cached image. If you set Image Pull Policy to Always, ACK pulls the image from the repository each time the application is deployed. This ensures that the latest image and code are used.
        -   **Set Image Pull Secret**: If you use a private image, we recommend that you configure a Secret to secure the image. For more information, see [Use an image secret](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image secret.md).
        -   **Resource Limit**: the upper limits of CPU and memory resources that can be used by the application. This prevents the application from using unnecessary resources. CPU is measured in millicores. Each millicore is one thousandth of one core. Memory is measured in bytes, which can be GiB, MiB, or KiB.
        -   **Required Resources**: The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from being unavailable when other services or processes occupy these resources.
        -   **Container Start Parameter**: Select **stdin** to send the input from the ACK console to the container. Select **tty** to specify that start parameters defined in a virtual terminal are imported to the ACK console.
        -   **Privileged Container**: If you select this check box, the setting of privileged=true is used for the container and the privilege mode is enabled. If you do not select this check box, the setting of privileged=false is used for the container and the privilege mode is disabled.
        -   **Init Container**: If you select this check box, the system creates an init container that contains useful tools. For more information, see [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/).
        -   **Port**: Set the host port and container port. TCP and UDP are supported.
        ![Set container basic parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0145359951/p13606.png)

    2.  Set the **Environment Variable** field.

        You can use key-value pairs to set environment variables for pods. Environment variables are used to apply pod configurations to containers. For more information, see [Expose Pod Information to Containers Through Environment Variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    3.  Configure **health checks**.

        You can set liveness probes and readiness probes. Liveness probes determine when to restart the container. Readiness probes determine whether the container is ready to start accepting traffic. For more information, see [Health checks](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes).

        ![Configure the health check feature](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9565359951/p10977.png)

        |Request type|Description|
        |------------|-----------|
        |HTTP request|Sends an HTTP GET request to the container. You can set the following parameters:        -   Protocol: HTTP or HTTPS.
        -   Path: the requested path on the server.
        -   Port: the container port that you want to open. Enter a port number that ranges from 1 to 65535.
        -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. Key-value pairs are supported.
        -   Initial Delay \(s\): the **initialDelaySeconds** field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 15.
        -   Period \(s\): the **periodSeconds** field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the **timeoutSeconds** field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |
        |TCP connection|Opens a TCP socket to the container. The kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. Supported parameters include:        -   Port: the container port that you want to open. Enter a port number that ranges from 1 to 65535.
        -   Initial Delay \(s\): the **initialDelaySeconds** field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 15.
        -   Period \(s\): the **periodSeconds** field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the **timeoutSeconds** field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |
        |Command line|Runs a probe command in the container to check the health status of the container. Supported parameters include:        -   Command: the probe command that is run to check the health status of the container.
        -   Initial Delay \(s\): the **initialDelaySeconds** field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 15.
        -   Period \(s\): the **periodSeconds** field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the **timeoutSeconds** field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |

    4.  Configure lifecycle events.

        You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Attach Handlers to Container Lifecycle Events](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Start**: Set the command and parameter that take effect before the container starts.
        -   **Post Start**: Set the command that takes effect after the container starts.
        -   **Pre Stop**: Set the command that takes effect before the container stops.
    5.  Configure volumes.

        Local storage and cloud storage are supported.

        -   **Local Storage**: supports hostPath, ConfigMap, Secret, or EmptyDir. The source directory or file is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **Cloud Volume**: supports Alibaba Cloud disk, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\).
    6.  Configure **Log Service**. You can set log collection parameters and customize tags.

        **Note:** Ensure that a Kubernetes cluster is deployed and the Log Service agent is installed in the cluster.

        Log collection parameters include:

        -   **Logstore**: the Logstore to be generated in Log Service for storing collected logs.
        -   **Log Path in Container**: You can set this parameter to stdout or a log path.
            -   **stdout**: collects standard outputs of the container.
            -   **Log path**: collects the text logs in the specified path. Wildcards can be used to set the log path.
        You can also set custom tags. Custom tags are added to the logs of the container when the logs are collected. Custom tags simplify how you collect log statistics, filter logs, and analyze logs.

7.  Set other parameters based on your needs and click **Next**.

8.  Configure advanced settings.

    You can configure **Job settings**.

    |Log field|Description|
    |---------|-----------|
    |**Completions**|The number of pods that the configured Job must successfully run. Default value: 1.|
    |**Parallelism**|The number of pods that the configured Job must run in parallel at any time. Default value: 1.|
    |**ActiveDeadlineSeconds**|The operating time limit of the configured Job. If the Job is not completed within the time limit, the system terminates the Job.|
    |**BackoffLimit**|The number of retries performed by the configured Job to create pods after a failure. Each time the Job fails, the failed pods associated with the Job are restarted with time delay. The time delay increases exponentially until a maximum of six minutes. Default value: 6.|
    |**Restart**|Only Never and OnFailure restart policies are supported.|

    ![Parameters on the Advanced tab](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1145359951/p13639.png)

9.  Click **Create**.

10. After the application is created, you are redirected to the Complete page. On this page, you can view the resource objects for the application.

    ![Creation complete](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1145359951/p13640.png)

    You can click **View Details** to view application details.

    During creation, you can view the creation status of pods in the **Status** column. In this example, two pods are created in parallel based on the Job definition.

    Wait until all pods are created.

11. Click **Back** in the upper-left corner of the page. On the Jobs page, you can view the time when the Job is ended.

    **Note:** If the pods associated with the Job are not complete, the page does not display the end time of the Job.


## What to do next

In the left-side navigation pane of the ACK console, click **Clusters** to go to the Clusters page. Find the cluster that you want to manage, and click the cluster name or click **Details** in the **Actions** column of the cluster. The Cluster Information page appears. In the left-side navigation pane, click **Workload**. The Deployments tab appears. Click the **Jobs** tab, find the application that you want to manage, and then click the application name, or click **Details** in the **Actions** column of the application. On the application details page, you can scale the application, view the YAML file, or refresh the application.

-   Scaling: On the application details page, click **Scale**. In the dialog box that appears, you can modify the number of required pods.
-   View the YAML file: On the application details page, click **View in YAML**. In the dialog box that appears, you can update or download the YAML file, or save the YAML file as another file.
-   Refresh: On the application details page, click **Refresh** to refresh the application.


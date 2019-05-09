# Create a Job application by using an image {#task_o24_h1s_3fb .task}

By running a Kubernetes cluster with Alibaba Cloud Container Service, you can create a Job application through the Web interface. This example creates a Job application named busybox to describe features of the Job application features.

You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

A Job processes short-lived one-off tasks in batches to guarantee that one or multiple pods in the batch tasks successfully terminate.

Kubernetes supports the following types of Jobs:

-   Non-parallel Job: A Job of this type creates only one pod. The Job is completed when the pod terminates successfully.
-   Job with a fixed completion count: A Job of this type has `.spec.completions` set to create multiple pods. The Job is completed when the number of these pods reaches the `.spec.completions` value.
-   Parallel Job with a work queue: A Job of this type has `.spec.Parallelism` set but has `.spec.completions` not set. The Job is completed when at least one pod has terminated with success, and all pods are terminated.
-   Parallel Job with a fixed completion count: A Job of this type has both `.spec.completions` and `.spec.Parallelism` set. Multiple pods of the Job process the work queue at the same time.

According to the `.spec.completions` and `.spec.Parallelism` settings, Jobs can be classified into the following patterns.

**Note:** The Job created in this example is a parallel Job with a fixed completion count.

|Job pattern|Usage example|Action|Completion|Parallelism|
|-----------|-------------|------|----------|-----------|
|One-off Job|Database migration|A Job creates a pod and the Job is completed when the pod terminates successfully.|1|1|
|Job with a fixed completion count|Pod that processes the work queue|A Job creates pods one by one. When the pods terminate successfully and the number of the terminated pods reaches the completions value, the Job is completed.|2+|1|
|Parallel Job with a fixed completion count|Multiple pods process work queues at the same time|A Job creates pods one by one. When the number of pods reaches the completions value, the Job is completed.|2+|2+|
|Parallel Job|Multiple pods process work queues at the same time|A Job creates one or multiple pods. When at least one pod terminates successfully, the Job is completed.|1|2+|

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under Kubernetes, choose **Application** \> **Job**, and then click **Create by Image** in the upper-right corner.
3.  Set the basic parameters and then click **Next**. 

    -   **Name**: Enter a name for the application.
    -   **Cluster**: Select a cluster to which the application is deployed.
    -   **Namespace**: Select a namespace in which the application deployment is located. You can also choose to use the default namespace.
    -   **Type**: Select the **Job** type.

        **Note:** In this example, select the **Job** type.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155739616913569_en-US.png)

4.  Configure containers. 

    **Note:** You can configure multiple containers for the pods of the application.

    1.  Set the container parameters. 

        -   **Image Name**: Click **Select image** to select an image in the displayed dialog box and then click **OK**. In this example, select the busybox image.

            You can also enter a private registry in the format of `domainname/namespace/imagename:tag` to specify an image.

        -   **Image Version**: Click **Select image version** to select a version. If you do not specify any image version, the system uses the latest version by default.
        -   **Always pull image**: Container Service caches the image to improve deployment efficiency. During deployment, if the tag of the newly specified image is the same as that of the cached image, Container Service reuses the cached image rather than pulls the same image again. Therefore, if you do not modify the image tag during scenarios where you are changing your code and image, the earlier image in the local cache is used in the application deployment. If you select this check box, Container Service ignores the cached image and re-pulls the image when deploying the application to make sure the latest image and code are always used.
        -   **Image pull secret**: If you use a private image, we recommend that you use a secret to guarantee the security of your image. For more information, see [Use an image secret](reseller.en-US/User Guide/Kubernetes cluster/Application management/Use an image secret.md#).
        -   **Resource Limit**: Specify the upper limit for the resources \(CPU and memory\) that can be used by this application to avoid occupying excessive resources. CPU is measured in millicores, that is, one thousandth of one core. Memory is measured in bytes, which can be Gi, Mi, or Ki.
        -   **Resource Request**: Specify how many resources \(CPU and memory\) are reserved for the application \(that is, these resources become exclusive to the container\). If you do not set this parameter, other services or processes will compete for resources, which means the application may become unavailable due to resource shortage.
        -   **Init Container**: Select this check box to create an Init Container that contains useful tools. For more information, see [https://kubernetes.io/docs/concepts/workloads/pods/init-containers/](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/).
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155739616913606_en-US.png)

    2.  Set **Environment**. 

        You can use key-value pairs to set environment variables for the pods. Environment variables are used to add environment labels or pass configurations for the pods. For more information, see [Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    3.  Set **Health Check**. 

        You can set liveness probes and readiness probes. Liveness probes are used to detect when to restart the container. Readiness probes determine if the container is ready to receive traffic. For more information about health check, see [https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/155739616910977_en-US.png)

        |Request method|Description|
        |--------------|-----------|
        |HTTP request|With this health check method, you can send an HTTP GET request to the container. The following parameters are supported:         -   Protocol: HTTP/HTTPS.
        -   Path: path to access the HTTP server.
        -   Port: number or name of the access port exposed by the container. The port number must be in the range of 1 to 65535.
        -   HTTP Header: custom headers in the HTTP request. HTTP allows repeated headers. You can use a key-value pair to set an HTTP Header.
        -   Initial Delay \(in seconds\): namely, the **initialDelaySeconds**, indicating the number of seconds for which the first probe must wait after the container is started. The default value is 3.
        -   Period \(in seconds\): namely, the **periodseconds**, indicating the interval at which probes are performed. The default value is 10. The minimum value is 1.
        -   Timeout \(in seconds\): namely, the **timeoutSeconds**, indicating the number of time that the probe has timed out. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes needed for determining a probe success after a failed probe. The default value is 1 and the minimum value is 1. It must be set to 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes needed for determining a probe failure after a successful probe. The default value is 3. The minimum value is 1.
 |
        |TCP connection|If you use this health check method, a TCP socket is sent to the container. The kubelet then attempts to open the socket of the container on a specified port. If a connection can be established, the container is considered healthy. If not, it is considered unhealthy. The following parameters are supported:         -   Port: number or name of the access port exposed by the container. The port number must be in the range of 1 to 65535.
        -   Initial Delay \(in seconds\): namely, the **initialDelaySeconds**, indicating the seconds for the first liveness or readiness probe must wait for after the container is started. The default is is 15.
        -   Period \(in seconds\): namely, the **periodseconds**, indicating the interval at which probes are performed. The default value is 10. The minimum value is 1.
        -   Timeout \(in seconds\): namely, the **timeoutSeconds**, indicating the number of time that the probe has timed out. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes needed for determining a probe success after a failed probe. The default value is 1 and the minimum value is 1. It must be set to 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes needed for determining a probe failure after a successful probe. The default value is 3. The minimum value is 1.
 |
        |Command line|With this heath check method, you can detect the container health by executing a probe detection command in the container. The following parameters are supported:         -   Command: a probe command used to detect the health of the container.
        -   Initial Delay \(in seconds\): namely, the **initialDelaySeconds**, indicating the number of seconds for which the first liveness or readiness probe must wait after the container is started. The default value is 5.
        -   Period \(in seconds\): namely, the **periodseconds**, indicating the interval at which probes are performed. The default value is 10. The minimum value 1.
        -   Timeout \(in seconds\): namely, the **timeoutSeconds**, indicating the number of time that the probe has timed out. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes needed for determining a probe success after a failed probe. The default value is 1 and the minimum value is 1. It must be set to 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes needed for determining a probe failure after a successful probe. The default value is 3. The minimum value is 1.
 |

    4.  Set the life cycle. 

        You can set the following parameters for the container life cycle: container config, start, post start, and pre-stop. For more information, see [https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Container Config**: You can select the stdin check box to enable standard input for the container, or select the tty check box to assign a virtual terminal to the container to send signals to the container. You can also select the two options at the same time. That is, you can bind the terminal \(tty\) to the container standard input \(stdin\). For example, an interactive program can obtain standard input from you and then display the obtained standard input in the terminal.
        -   **Start**: Set a pre-start command and parameter for the container.
        -   **Post Start**: Set a post-start command for the container.
        -   **Pre Stop**: Set a pre-stop command for the container.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155739616913638_en-US.png)

    5.  Set data volumes. 

        You can configure local storage and cloud storage.

        -   **Local storage**: Supported storage types include HostPath, ConfigMap, Secret, and EmptyDir. By setting a type of local storage, you can mount its mount source to the container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **Cloud storage**: Supported types of cloud storage include cloud disks, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\).
    6.  Set **Log Service**. You can set collection parameters and customize tags. 

        **Note:** Make sure that you have deployed a Kubernetes cluster and installed the log plugin on the cluster.

        Set the following log collection parameters:

        -   **Log Store**: Set a Logstore. After you specify the Logstore name, the Logstore is generated in Log Service to store collected logs.
        -   **Log path in the container**: You can set this parameter to stdout or set a log path.
            -   **stdout**: If you set a log path to stdout, you can collect the standard output logs of the container.
            -   **text log**: If you set a container log path, you can collect the text logs of the path. Wildcards can be used in setting the log file name for a log path.
        You can also set custom tags. The custom tags are collected to the container output logs. A custom tag can help you tag container logs, making it easy to collect log statistics, filter logs, and analyze logs by using other methods.

5.  After you complete the container configuration, click **Next**.
6.  Configure advanced settings. 

    You can configure **Job Settings**.

    |Parameter|Description|
    |---------|-----------|
    |**Completions**|Number of pods that must be run successfully by the configured Job. The default value is 1.|
    |**Parallelism**|Number of pods that must be run in parallel by the configured Job at any time. The default value is 1.|
    |**ActiveDeadlineSeconds**|Operating time limit of the configured Job. If the Job is not completed within the time limit, the system tries to terminate the Job.|
    |**BackoffLimit**|Number of retries performed by the configured Job to create pods after a failure. The default is 6. Each time the Job fails, the failed pods associated with the Job are recreated with time delay. The time delay grows exponentially each time. The upper limit of the time delay is six minutes.|
    |**Restart**|Only Never and OnFailure restart policies are supported.|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155739616913639_en-US.png)

7.  Click **Create**.
8.  After you create the Job application, a new page is displayed by default to prompt that you have created the application with the objects included. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155739616913640_en-US.png)

    You can click **View Details** to view the Job details.

    During the creation process, you can view the creation status of the pods in the **Status** column. In this example, two pods are created in parallel according to the Job definition.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155739616913641_en-US.png)

    Wait until all pods are created.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155739616913642_en-US.png)

9.  In the upper-left corner, click **Back to List**. On the Jog page, the Job completion time is displayed. 

    **Note:** If the Job has not created all the pods, the page does not display the Job completion time.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/23357/155739617013643_en-US.png)



# Create a deployment application by using an image {#task_p2s_2rl_vdb .task}

This topic describes how to use an image to create a deployment application. In this topic, an Nginx application that is accessible to the Internet is created.

A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](intl.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).
2.  In the left-side navigation pane under Kubernetes, choose **Application** \> **Deployment**, and then click **Create by Image** in the upper-right corner.
3.  Set **Name**, **Cluster**, **Namespace**, **Replicas**, **Type**, **Tag**, and **Annotation**. The replicas parameter indicates the number of pods contained in the application. Then click **Next**. 

    **Note:** In this example, you need to select the **Deployment** type.

    If you do not set **Namespace**, the system automatically uses the default namespace.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201510973_en-US.png)

4.  Configure a container. 

    **Note:** You can configure multiple containers for the pod of the application.

    1.  Set general container parameters. 

        -   **Image Name**: Click **Select image** to select the image in the displayed dialog box and then click **OK**. In this example, select the Nginx image.

            You can also enter a private registry in the format of `domainname/namespace/imagename:tag` to specify an image.

        -   **Image Version**: Click **Select image version** to select a version. If you do not select an image version, the system uses the latest version by default.
        -   **Always pull image**: Container Service caches the image to improve deployment efficiency. During deployment, if the tag of the newly specified image is the same as that of the cached image, Container Service reuses the cached image, instead of re-pulling the same image. Therefore, if you do not modify the image tag when changing your code and image, the early image in the local cache is used in the application deployment. If you select this check box, Container Service ignores the cached image and re-pulls an image when deploying the application to make sure the latest image and code are always used.
        -   **Image pull secret**: Create a Secret for the image. A secret is required to pull a image from a private image repository. For more information, see [Use an image Secret](intl.en-US/User Guide/Kubernetes cluster/Application management/Use an image Secret.md#).
        -   **Resource Limit**: Specify the upper limit for the resources \(CPU and memory\) that can be used by this application to avoid occupying excessive resources. CPU is measured in the number of cores. Memory is measured in bytes, which can be MiB.
        -   **Resource Request**: Specify how many resources \(CPU and memory\) are reserved for the application. These resources can be set to be exclusive to the container by using this parameter. If you do not set this parameter, other services or processes will compete for resources. Then the application may become unavailable due to resource shortage.
        -   **Init Container**: Select this check box to create an Init Container that contains useful tools. For more information, see [Init containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/).
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201510974_en-US.png)

    2.  Set environment variables. 

        You can use key-value pairs to set environment variables for the pods. Environment variables are used to add environment labels or pass configurations for the pods. For more information, see [Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    3.  Set health checks. 

        You can set liveness probes and readiness probes. Liveness probes are used to detect when to restart the container. Readiness probes determine if the container is ready to receive traffic. For more information about health checks, see [Configure liveness and readiness probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201610977_en-US.png)

        |Request method|Description|
        |--------------|-----------|
        |HTTP request|With this health check method, you can send an HTTP GET request to the container. The following parameters are supported:         -   Protocol: HTTP/HTTPS.
        -   Path: the path to access the HTTP server.
        -   Port: the number or name of the access port exposed by the container. The port number must be in the range of 1 to 65535.
        -   HTTP Header: custom headers in the HTTP request. HTTP allows repeated headers. You can use a key-value pair to set an HTTP Header.
        -   Initial Delay \(in seconds\): the **initialDelaySeconds** parameter, indicating the number of seconds for which the first probe must wait after the container is started. The default value is 3.
        -   Period \(in seconds\): the **periodseconds** parameter, indicating the interval at which probes are performed. The default value is 10. The minimum value is 1.
        -   Timeout \(in seconds\): the **timeoutSeconds** parameter, indicating the number of time that the probe has timed out. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes needed for determining a probe success after a failed probe. The default value is 1 and the minimum value is 1. It must be set to 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes needed for determining a probe failure after a successful probe. The default value is 3. The minimum value is 1.
 |
        |TCP connection|If you use this health check method, a TCP socket is sent to the container. The kubelet then attempts to open the socket of the container on a specified port. If a connection can be established, the container is considered healthy. If not, it is considered unhealthy. The following parameters are supported:         -   Port: the number or name of the access port exposed by the container. The port number must be in the range of 1 to 65535.
        -   Initial Delay \(in seconds\): the **initialDelaySeconds** parameter, indicating the seconds for the first liveness or readiness probe must wait for after the container is started. The default value is 15.
        -   Period \(in seconds\): the **periodseconds** parameter, indicating the interval at which probes are performed. The default value is 10. The minimum value is 1.
        -   Timeout \(in seconds\): the **timeoutSeconds** parameter, indicating the number of time that the probe has timed out. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes needed for determining a probe success after a failed probe. The default value is 1 and the minimum value is 1. It must be set to 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes needed for determining a probe failure after a successful probe. The default value is 3. The minimum value is 1.
 |
        |Command line|With this heath check method, you can detect the container health by executing a probe detection command in the container. The following parameters are supported:         -   Command: a probe command used to detect the container health.
        -   Initial Delay \(in seconds\): the **initialDelaySeconds** parameter, indicating the number of seconds for which the first liveness or readiness probe must wait after the container is started. The default value is 5.
        -   Period \(in seconds\): the **periodseconds** parameter, indicating the interval at which probes are performed. The default value is 10. The minimum value 1.
        -   Timeout \(in seconds\): the **timeoutSeconds** parameter, indicating the number of time that the probe has timed out. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes needed for determining a probe success after a failed probe. The default value is 1 and the minimum value is 1. It must be set to 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes needed for determining a probe failure after a successful probe. The default value is 3. The minimum value is 1.
 |

    4.  Set life cycle rules. 

        You can set the following parameters for the container life cycle: start, post start, and pre-stop. For more information, see [Attach handlers to container lifecycle events](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Start**: Set a pre-start command and parameter for the container.
        -   **Post Start**: Set a post-start command for the container.
        -   **Pre Stop**: Set a pre-stop command for the container.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201611371_en-US.png)

    5.  Set volumes. 

        You can configure local storage and cloud storage.

        -   **local storage**: Supported storage types include HostPath, ConfigMap, Secret, and EmptyDir. By setting a type of local storage, you can mount its mount source to the container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **cloud storage**: Supported types of cloud storage include disks, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\).
        This example sets a disk as the volume and mounts the disk to the /tmp container path. Then container data generated in this path is stored to the disk.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201610976_en-US.png)

    6.  Set **Log Service**. You can set collection parameters and customize tags. 

        **Note:** Make sure that you have deployed a Kubernetes cluster and installed the log plugin on the cluster.

        Set the following log collection parameters:

        -   **Log Store**: Set a Logstore. After you specify the Logstore name, the Logstore is generated in Log Service to store collected logs.
        -   **Log path in the container**: Set this parameter to stdout or set a log path.
            -   **stdout**: If you set the log path parameter to stdout, you can collect the standard output logs of the container.
            -   **text log**: If you specify a container log path, you can collect the text logs of the path. Wildcards can be used in setting the log file name for a log path. In this example, text logs in the path of /var/log/nginx are collected.
        You can also customize log tags. The customized log tags can be collected together with container output logs and can benefit log analysis actions such as collecting log statistics and filtering specific logs.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201611364_en-US.png)

5.  Click **Next**.
6.  Configure advanced settings. 
    1.  Set **Access Control**. You can set the methods to expose the application pod and then click **Create**. In this example, a cluster IP service and an Ingress are set to create an Nginx application that is accessible for the Internet.

        **Note:** 

        You can set access methods according to the communication requirements of your application.

        -   Internal application: an application that works only inside the cluster. You can create a cluster IP service or a node port service as needed for communication within the cluster.
        -   External application: an application that needs to be exposed to the Internet. You can set how the application is accessed by using either of the following two methods:
            -   Create a Server Load Balancer service. This method uses Alibaba Cloud Server Load Balancer \(SLB\) to provide Internet accessibility for the application.
            -   Create a cluster IP service or a node port service, and create an Ingress. This method provides Internet accessibility through the Ingress. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/).
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201610979_en-US.png)

        1.  Click **Create** on the right of **Service**. Configure a service in the displayed dialog box, and then click **Create**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201610980_en-US.png)

            -   **Name**: Enter the service name. The default is `applicationname-svc`.
            -   **Type**: Select one service type.
                -   **Cluster IP**: Exposes the service by using the internal IP address of your cluster. If you select this service type, the service is accessible only within the cluster.
                -   **Node port**: Exposes the service by using the IP address and the static port \(NodePort\) of each node. A node port service routes to a cluster IP service that is automatically created. You can access the node port service from outside the cluster by requesting `<NodeIP>:<NodePort>`.
                -   **Server Load Balancer**: Alibaba Cloud Server Load Balancer service. With this type of service, you can set an Internet or intranet access method for your application. SLB can route to a node port service and a cluster IP service.
            -   **Port Mapping**: Add a service port and a container port, and select the TCP or UDP protocol. If you select the node port **Type**, you must add a node port to avoid port conflict.
            -   **annotation**: Add an annotation to the service. You can set SLB parameters. For more information, see [Access services by using Server Load Balancer](intl.en-US/User Guide/Kubernetes cluster/Network management/Access services by using Server Load Balancer.md#).
            -   **Tag**: Add a tag to the service to identify the service.
        2.  Click **Create** on the right of **Ingress**. In the displayed dialog box, configure an Ingress rule for the application pod, and then click **Create**. For more information, see [Ingress configurations](intl.en-US/User Guide/Kubernetes cluster/Network management/Ingress configurations.md#).

            **Note:** When you create an application by using an image, you can create an Ingress rule for only one service. In this example, a virtual host name is used as the test domain name. You need to add a record to the host. You must use a filing domain name when you create your application.

            ``` {#codeblock_evx_304_uw4}
            101.37.224.146   foo.bar.com    #This is the IP address of the Ingress.
            ```

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201710981_en-US.png)

        3.  In the access control area, the created service and Ingress are displayed. You can perform further configurations by clicking **Update** or **Delete**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201710982_en-US.png)

    2.  Set **Horizontal Pod Autoscaling \(HPA\)**. 

        You enable HPA by selecting the **Enable** check box. Alibaba Cloud Container Service for Kubernetes provides pod auto scaling to deal with different application workloads. That is, you can change the number of pods according to the container CPU and memory usage.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201710978_en-US.png)

        **Note:** To use this function, you must set required resources for the pod. Otherwise, pod auto scaling cannot take effect. For more information, see general container settings.

        -   **Metric**: resource type. CPU or memory is available. This parameter must be specified with a resource type that is the same as the required resource type.
        -   **Condition**: the percentage value of resource usage. The number of containers increases when the resource usage exceeds this value.
        -   **Maximum Replicas**: the maximum number of the containers that the deployment can include.
        -   **Minimum Replicas**: the minimum number of the containers that the deployment can include.
    3.  Set **Scheduling**. 

        You can set an update method, node affinity, pod affinity, and pod anti affinity. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).

        **Note:** Affinity scheduling depends on node tags and pod tags. You can use built-in or customized tags to schedule nodes or pods.

        1.  Set **Update Method**.

            You can select the `RollingUpdate` or `Recreate` \(**OnDelete**\) method to replace old pods with new ones. For more information, see [Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/).

        2.  Set **Node Affinity** by using node tags.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201711137_en-US.png)

            Required rules and preferred rules are supported, and available operators include `In, NotIn, Exists, DoesNotExist, Gt, and Lt` .

            -   **Required** rules must be satisfied and correspond to requiredDuringSchedulingIgnoredDuringExecution. The required rules have the same effect as `NodeSelector`. In this example, the pod can be scheduled to only a node with the specified tags.

                You can add multiple required rules, but only one required rule needs to be satisfied for pod scheduling.

            -   **Preferred** rules can be unnecessarily satisfied and correspond to preferredDuringSchedulingIgnoredDuringExecution. With the scheduling setting in this example, the system tries not to schedule the pod to the nodes with the specified tag.

                You can also set **Weight** for each preferred rule. If multiple nodes satisfies the preferred rules, the system schedules the pod to a node with the highest weight.

                You can add multiple preferred rules, and all the rules must be satisfied for pod scheduling.

        3.  Set **Pod Affinity** to deploy the application pod in a topology domain together with other pods. For example, to reduce network latency between the services that communicate with each other, you can deploy their pods to a topology domain \(for example, a host\).

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201711222_en-US.png)

            You can schedule pods according to tags of pods running on nodes. Required rules and preferred rules are supported, and available operators include `In, NotIn, Exists, DoesNotExist`.

            -   **Required** rules must be satisfied and correspond to requiredDuringSchedulingIgnoredDuringExecution. All specified conditions of required rules must be met for pod affinity scheduling.
                -   **Namespace**: Set a namespace. This parameter is required because the scheduling policy is based on pod tags.
                -   **Topology Key**: Set a topology domain to which pods are scheduled. This parameter takes effect through node tags. For example, if you set `kubernetes.io/hostname` as the topology key, a node is used to identify a topology. If you set `beta.kubernetes.io/os` as the topology key, a node operating system is used to identify a topology.
                -   **Selector**: Click this button to add a required rule.
                -   **View Applicaiton List**: Click **View Applicaiton List**, a dialog box is displayed. In the dialog box, you can view applications in each namespace and export application tags to the dialog box in which you set pod affinity.
                -   Required rule tag: Set a tag name, its operator, and the tag value for existing applications. This example schedules the application to be created to a host on which applications tagged with `app: nginx` run.
            -   **Preferred** rules can be unnecessarily satisfied and correspond to preferredDuringSchedulingIgnoredDuringExecution. Specified conditions of required rules will be met as many as possible for pod affinity scheduling.

                You can set **Weight** for each preferred rule. The weight value range is 1 to 100. If multiple nodes satisfies the preferred rules, the system schedules the pod to a node with the highest weight. Other parameters are the same with the required rule setting.

        4.  Set **Pod Anti Affinity** to deploy the application pods in a topology domain that excludes other pods. Scenarios that use pod anti affinity scheduling include:

            -   Distribute the pods of a service to different topology domains \(for example, different hosts\) to improve the service stability.
            -   Grant a pod the exclusive access to a node so as to guarantee that no other pods use the resources of the node.
            -   Distribute pods of the services that may affect each other to different hosts.
            **Note:** You can set pod anti affinity scheduling by using the same method as setting pod affinity scheduling. But the same scheduling rules have different meanings for these two types of scheduling. You need to select appropriate scheduling rules as needed.

7.  Click **Create**.
8.  After you create the application, anew page is displayed by default to prompt that you have created the application and lists objects included in the application.You can click **View detail** to view the deployment details. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201710983_en-US.png)

    The nginx-deployment page is displayed by default.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201810984_en-US.png)

9.  Choose **Discovery and Load Balancing** \> **Ingress** to verify that a rule is displayed in the Ingress list. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201810985_en-US.png)

10. Access the test domain name in your browser to verify that you can visit the Nginx welcome page. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156396201810986_en-US.png)



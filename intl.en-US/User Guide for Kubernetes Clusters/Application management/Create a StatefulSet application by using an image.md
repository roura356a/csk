# Create a StatefulSet application by using an image

Kubernetes clusters of Alibaba Cloud Container Service allows you to quickly create applications of the StatefultSet type through the web interface. In this example, create a StatefultSet Nginx application and show features of a StatefultSet application.

-   You have created a Kubernetes cluster. For more information, see [Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md).
-   You have successfully created a cloud disk storage volume claim. For more information, see [Create a Persistent Volume Claim](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Create a Persistent Volume Claim.md).
-   You have successfully connected to the master node of the Kubernetes cluster. For more information, see [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md).

StatefulSet features are as follows:

|Scenarios|Description|
|---------|-----------|
|Pod consistency|Contains order \(such as startup and stop order\) and network consistency. This consistency is related to pods and has nothing to do with the node to which the pods are to be scheduled.|
|Stable persistent storage|Create a PV for each pod through VolumeClaimTemplate. Deleting or reducing replicas does not delete relevant volumes.|
|Stable network marker|The hostname mode for a pod is: `(statefulset name)-(sequence number)`.|
|Stable order|For StatefulSet of N replicas, each pod is assigned a unique order number within the range of 0 to N.|

1.  Log on to the [Container Service console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Application** \> **StatefulSet**. Then, click **Create from Image** in the upper-right corner.

3.  Configure the basic parameters and then click **Next**.

    -   **Name**: Enter the application name.
    -   **Cluster**: Select a cluster to which the application is deployed.
    -   **Namespace**: Select a namespace in which the application deployment is located. By default, the default namespace is used.
    -   **Replicas**: Set the number of pods included in the application.
    -   **Type**: Deployment type and StatefulSet type are available.

        **Note:** In this example, select the **StatefulSet** type.

    -   **Label**: Add a label to the application.
    -   **Annotations**: Add a annotation to the application.
    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7045359951/p12304.png)

4.  Configure containers.

    **Note:** You can configure multiple containers for the pod of the application.

    1.  Configure the general settings for the application.

        -   **Image Name**: Click **Select image** to select the image in the displayed dialog box and then click **OK**. In this example, select the nginx image.

            You can also enter the private registry in the format of `domainname/namespace/imagename:tag` to specify an image.

        -   **Image Version**: Click **Select image version** to select a version. If the image version is not specified, the system uses the latest version by default.
        -   **Always pull image**: Container Service caches the image to improve deployment efficiency. During deployment, if the image tag is found consistent with that on the local cache, the image on the local cache is reused and is not pulled again. Therefore, if you do not modify the image tag when changing your codes and image for convenience of upper-layer business, the early image on the local cache is used in the application deployment. With this check box selected, Container Service ignores the cached image and re-pulls the image from the repository when deploying the application to make sure the latest image and codes are always used.
        -   **Resource Limit**: Specify the upper limit for the resources \(CPU and memory\) that can be used by this application to avoid occupying excessive resources. CPU is measured in millicores, that is, one thousandth of one core. Memory is measured in bytes, which can be Gi, Mi, or Ki.
        -   **Resource Request**: Specify how many resources \(CPU and memory\) are reserved for the application, that is, these resources are exclusive to the container. Other services or processes will compete for resources when the resources are insufficient. By specifying the Resource Request, the application will not become unavailable because of insufficient resources.
        -   **Init Container**: Selecting this check box creates an Init Container which contains useful tools. For more information, see [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/).
        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7045359951/p12305.png)

    2.  Configure **Environment**.

        You can configure environment variables for the pod by using key-value pairs. Environment variables are used to add environment labels or pass configurations for the pod. For more information, see [Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    3.  Configure **Health Check**.

        The health check function includes liveness probes and readiness probes. Liveness probes are used to detect when to restart the container. Readiness probes determine if the container is ready for receiving traffic. For more information about health check, see [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes).

        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9565359951/p10977.png)

        |Request method|Description|
        |--------------|-----------|
        |HTTP request|An HTTP GET request is sent to the container. The following are supported parameters:         -   Protocol: HTTP/HTTPS
        -   Path: Path to access the HTTP server
        -   Port: Number or name of the port exposed by the container. The port number must be in the range of 1 to 65535.
        -   HTTP Header: Custom headers in the HTTP request. HTTP allows repeated headers. Supports the correct configuration of key values.
        -   Initial Delay \(in seconds\): Namely, the **initialDelaySeconds**. Seconds for the first probe has to wait after the container is started. The default is 3.
        -   Period \(in seconds\): Namely, the **periodseconds**. Intervals at which the probe is performed. The default value is 10. The minimum value is 1.
        -   Timeout \(in seconds\): Namely, the **timeoutSeconds**. The time of probe timeout. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes that are considered as successful after a failed probe. The default is 1 and the minimum is 1. It must be 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes that are considered as failed after a successful probe. The default value is 3. The minimum value is 1. |
        |TCP connection|A TCP socket is send to the container. The kubelet attempts to open a socket to your container on the specified port. If a connection can be established, the container is considered healthy. If not, it is considered as a failure. The following are supported parameters:         -   Port: Number or name of the port exposed by the container. The port number must be in the range of 1 to 65535.
        -   Initial Delay \(in seconds\): Namely, the **initialDelaySeconds**. Seconds for the first liveness or readiness probe has to wait after the container is started. The default is 15.
        -   Period \(in seconds\): Namely, the **periodseconds**. Intervals at which the probe is performed. The default value is 10. The minimum value is 1.
        -   Timeout \(in seconds\): Namely, the **timeoutSeconds**. The time of probe timeout. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes that are considered as successful after a failed probe. The default is 1 and the minimum is 1. It must be 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes that are considered as failed after a successful probe. The default value is 3. The minimum value is 1. |
        |Command line|Detect the health of the container by executing probe detection commands in the container. The following are supported parameters:         -   Command: A probe command used to detect the health of the container.
        -   Initial Delay \(in seconds\): Namely, the **initialDelaySeconds**. Seconds for the first liveness or readiness probe has to wait after the container is started. The default is 5.
        -   Period \(in seconds\): Namely, the **periodseconds**. Intervals at which the probe is performed. The default value is 10. The minimum value 1.
        -   Timeout \(in seconds\): Namely, the **timeoutSeconds**. The time of probe timeout. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes that are considered as successful after a failed probe. The default is 1 and the minimum is 1. It must be 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes that are considered as failed after a successful probe. The default value is 3. The minimum value is 1. |

    4.  Configure the lifecycle rule.

        You can configure the following parameters for the container lifecycle: start, post start, and pre-stop. For more information, see[Attach Handlers to Container Lifecycle Events](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event) .

        -   **Start**: Configure a pre-start command and parameter for the container.
        -   **Post Start**: Configure a post-start command for the container.
        -   **Pre Stop**: Configure a pre-end command for the container.
        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p11371.png)

    5.  Configure data volumes.

        Local storage and cloud storage can be configured.

        -   **Local storage**: Supports hostPath, configmap, secret, and temporary directory. The local data volumes mount the corresponding mount source to the container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **Cloud storage**: Supports three types of cloud storage: cloud disks, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\).
        In this example, configure a data volume claim named disk-ssd of cloud disk type and mount it to the /data path.

        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8045359951/p12307.png)

    6.  Configure **Log Service**. You can configure collection methods and customize tags for this service.

        **Note:** Make sure that a Kubernetes cluster is deployed and that the log plug-in is installed on the cluster.

        Configure log collection methods as follows:

        -   **Log Store**: Configure a Logstore generated in Log Service which is used to store collected logs.
        -   **Log path in the container**: Supports stdout and text logs.
            -   **stdout**: Collects standard output logs of containers.
            -   **text log**: Collects logs in the specified path in the container. In this example, collect text logs in the path of /var/log/nginx. Wildcards are also supported.
        You can also set custom tags. The customized tags are collected to the container output logs. A custom tag can help you tag container logs, providing convenience to log analysis such as log statistics and filter.

        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8045359951/p11364.png)

5.  Click **Next** after completing the configurations.

6.  Configure advanced settings. In this example, configure only access settings.

    1.  Set **Access Control**.

        You can set the methods to expose the application pod and then click **Create**. In this example, a cluster IP service and an Ingress are set to create an Nginx application that is accessible for the Internet.

        **Note:**

        You can set access methods according to the communication requirements of your application.

        -   Internal application: an application that works only inside the cluster. You can create a cluster IP service or a node port service as needed for communication within the cluster.
        -   External application: an application that needs to be exposed to the Internet. You can set how the application is accessed by using either of the following two methods:
            -   Create a Server Load Balancer service. This method uses Alibaba Cloud Server Load Balancer \(SLB\) to provide Internet accessibility for the application.
            -   Create a cluster IP service or a node port service, and create an Ingress. This method provides Internet accessibility through the Ingress. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/).
        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10979.png)

        1.  Click **Create** on the right of **Service**. Configure a service in the displayed dialog box, and then click **Create**.

            ![create service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8045359951/p63160.png)

            -   **Name**: Enter the service name. The default is `applicationname-svc`.
            -   **Type**: Select one service type.
                -   **Cluster IP**: Exposes the service by using the internal IP address of your cluster. If you select this service type, the service is accessible only within the cluster.
                -   **Node port**: Exposes the service by using the IP address and the static port \(NodePort\) of each node. A node port service routes to a cluster IP service that is automatically created. You can access the node port service from outside the cluster by requesting `<NodeIP>:<NodePort>`.
                -   **Server Load Balancer**: Alibaba Cloud Server Load Balancer service. With this type of service, you can set an Internet or intranet access method for your application. SLB can route to a node port service and a cluster IP service.
            -   **Port Mapping**: Add a service port and a container port, and select the TCP or UDP protocol. If you select the node port **Type**, you must add a node port to avoid port conflict.
            -   **Annotation**: Add an annotation to the service. You can set SLB parameters. For more information, see [t16677.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).
            -   **Tag**: Add a tag to the service to identify the service.
        2.  Click **Create** on the right of **Ingress**. In the displayed dialog box, configure an Ingress rule for the application pod, and then click **Create**. For more information, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Ingress configurations.md).

            **Note:** When you create an application by using an image, you can create an Ingress rule for only one service. In this example, a virtual host name is used as the test domain name. You need to add a record to the host. You must use a filing domain name when you create your application.

            ```
            101.37.224.146   foo.bar.com    #This is the IP address of the Ingress.
            ```

            ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10981.png)

        3.  In the access control area, the created service and Ingress are displayed. You can perform further configurations by clicking **Update** or **Delete**.

            ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10982.png)

    2.  Set **Horizontal Pod Autoscaling \(HPA\)**.

        You enable HPA by selecting the **Enable** check box. Alibaba Cloud Container Service for Kubernetes provides pod auto scaling to deal with different application workloads. That is, you can change the number of pods according to the container CPU and memory usage.

        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10978.png)

        **Note:** To use this function, you must set required resources for the pod. Otherwise, pod auto scaling cannot take effect. For more information, see general container settings.

        -   **Metric**: resource type. CPU or memory is available. This parameter must be specified with a resource type that is the same as the required resource type.
        -   **Condition**: the percentage value of resource usage. The number of containers increases when the resource usage exceeds this value.
        -   **Maximum Replicas**: the maximum number of the containers that the StatefulSet application can contain.
        -   **Minimum Replicas**: the minimum number of the containers that the StatefulSet application can contain.
    3.  Set **Scheduling**.

        You can set an update method, node affinity, pod affinity, and pod anti affinity. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).

        **Note:** Affinity scheduling depends on node tags and pod tags. You can use built-in or customized tags to schedule nodes or pods.

        1.  Set **Update Method**.

            You can select the `RollingUpdate` or `Recreate` \(**OnDelete**\) method to replace old pods with new ones. For more information, see [Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/).

        2.  Set **Node Affinity** by using node tags.

            ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8045359951/p11137.png)

            Required rules and preferred rules are supported, and available operators include `In, NotIn, Exists, DoesNotExist, Gt, and Lt` .

            -   **Required** rules must be satisfied and correspond to requiredDuringSchedulingIgnoredDuringExecution. The required rules have the same effect as `NodeSelector`. In this example, the pod can be scheduled to only a node with the specified tags.

                You can add multiple required rules, but only one required rule needs to be satisfied for pod scheduling.

            -   **Preferred** rules can be unnecessarily satisfied and correspond to preferredDuringSchedulingIgnoredDuringExecution. With the scheduling setting in this example, the system tries not to schedule the pod to the nodes with the specified tag.

                You can also set **Weight** for each preferred rule. If multiple nodes satisfies the preferred rules, the system schedules the pod to a node with the highest weight.

                You can add multiple preferred rules, and all the rules must be satisfied for pod scheduling.

        3.  Set **Pod Affinity** to deploy the application pod in a topology domain together with other pods. For example, to reduce network latency between the services that communicate with each other, you can deploy their pods to a topology domain \(for example, a host\).

            ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p11222.png)

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

8.  After you create the application, the create success page is displayed by default and objects contained in the application are listed. You can click **View detail** to view the deployment details.

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8045359951/p12341.png)

    The StatefulSet page is displayed by default.

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8045359951/p12343.png)

9.  Then click **Back to list** in the upper-left corner to view the created StatefulSet application in the StatefulSet list page.

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9045359951/p12431.png)

10. To verify service scalability, click **Scale** at the right of a target nginx application.

    1.  In the displayed dialog box, set the number of pod to 3. You can see that when you expand pods, the pods are in the increment order; when you contract pods, the pods are in the descending order. This shows the order stability of pods in StatefulSet.

        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9045359951/p12438.png)

    2.  Click **Application** \> **Volumes Claim** in the left-side navigation pane, you can see that as the application expands, new cloud disk volumes are created with pods; if the application contracts, created PV/PVC will not be deleted.

        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9045359951/p12450.png)


Connect to the master node and run following commands to verify the persistent storage feature.

Create a temporary file on a cloud disk:

```
# kubectl exec nginx-1 ls /tmp            #list files under this directory
lost+found

# kubectl exec nginx-1 touch /tmp/statefulset         #add a temporty file named statefulset

# kubectl exec nginx-1 ls /tmp
lost+found
statefulset
```

Remove the pod to verify the data persistence:

```
# kubectl delete pod nginx-1
pod"nginx-1" deleted

# kubectl exec nginx-1 ls /tmp                         #data persistence storage
lost+found
statefulset
```

In addition, you can also find that after you delete a pod, the pod automatically restarts after a period of time, which indicates the high availability of the StatefulSet application.


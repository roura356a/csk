# Create a deployment application by using an image {#task_p2s_2rl_vdb .task}

You can use an image to create an Nginx application that is accessible for the Internet.

Create a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Application** \> **Deployment** in the left-side navigation pane, and then click **Create by image** in the upper-right corner. 
3.  Configure **Name**, **Cluster**, **Namespace**, **Replicas**, and **Type**. The configured value of the replicas parameter specifies the number of pods contained in the application. Click **Next**. 

    **Note:** In this example, select the **Deployment** type.

    If you do not configure **Namespace**, the system uses the default namespace by default.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210973_en-US.png)

4.  Configure containers. 

    **Note:** You can configure multiple containers for the pod of the application.

    1.  Configure the general settings for the application. 

        -   **Image Name**: Click **Select image** to select the image in the displayed dialog box and then click **OK**. In this example, select the nginx image.

            Besides, you can enter a private registry in the format of `domainname/namespace/imagename:tag` to specify an image.

        -   **Image Version**: Click **Select image version** to select a version. If you do not select an image version, the system uses the latest version by default.
        -   **Always pull image**: Container Service caches the image to improve deployment efficiency. During deployment, if the tag of the newly configured image is consistent with that of the cached image, Container Service reuses the cached image rather than pull the same image again. Therefore, if you do not modify the image tag when changing your codes and image for convenience of upper-layer business, the early image on the local cache is used in the application deployment. With this check box selected, Container Service ignores the cached image and re-pulls an image when deploying an application so as to make sure the latest image and codes are used.
        -   **Resource Limit**: Specify the upper limit for the resources \(CPU and memory\) that can be used by this application to avoid occupying excessive resources. CPU is measured in millicores, that is, one thousandth of one core. Memory is measured in bytes, which can be Gi, Mi, or Ki.
        -   **Resource Request**: Specify how many resources \(CPU and memory\) are reserved for the application, that is, these resources are exclusive to the container. Other services or processes will compete for resources when the resources are insufficient. By specifying the Resource Request, the application will not become unavailable because of insufficient resources.
        -   **Init Container**: Selecting this check box creates an Init Container which contains useful tools. For more information, see [https://kubernetes.io/docs/concepts/workloads/pods/init-containers/](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/).
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210974_en-US.png)

    2.  Configure data volumes. 

        Local storage and cloud storage can be configured.

        -   **Local storage**: Supports hostPath, configmap, secret, and temporary directory. The local data volumes mount the corresponding mount source to the container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **Cloud storage**: Supports three types of cloud storage: cloud disks, Network Attached Storage \(NAS\), and Object Storage Service \(OSS\).
        In this example, configure a cloud disk as the data volume and mount the cloud disk to the /tmp container path. Then container data generated in this path are stored to the cloud disk.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210976_en-US.png)

    3.  Configure **Log Service**. You can configure collection methods and customize tags for this service. 

        **Note:** Make sure that a Kubernetes cluster is deployed and that the log plug-in is installed on the cluster.

        Configure log collection methods as follows:

        -   **Log Store**: Configure a Logstore generated in Log Service which is used to store collected logs.
        -   **Log path in the container**: Supports stdout and text logs.
            -   **stdout**: Collects standard output logs of containers.
            -   **text log**: Collects logs in the specified path in the container. In this example, collect text logs in the path of /var/log/nginx. Wildcards are also supported.
        You can also configured custom tags. The customized tags are collected to the container output logs. A custom tag can help you tag container logs, providing convenience to log analysis such as log statistics and filter.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010211364_en-US.png)

    4.  Configure environment variables. 

        You can configure environment variables for the pod by using key-value pairs. Environment variables are used to add environment labels or pass configurations for the pod. For more information, see [Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    5.  Configure the lifecycle rule. 

        You can configure the following parameters for the container lifecycle: container config start, post start, and pre-stop. For more information, see [https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Container Config**: Select the stdin check box to enable standard input for the container. Select the tty check box to assign an virtual terminal to for the container to send signals to the container. These two options are usually used together, which indicates to bind the terminal \(tty\) to the container standard input \(stdin\). For example, an interactive program obtains standard input from you and then displays the obtained standard input in the terminal.
        -   **Start**: Configure a pre-start command and parameter for the container.
        -   **Post Start**: Configure a post-start command for the container.
        -   **Pre Stop**: Configure a pre-end command for the container.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010211371_en-US.png)

    6.  Configure **Health Check** 

        The health check function includes liveness probes and readiness probes. Liveness probes are used to detect when to restart the container. Readiness probes determine if the container is ready for receiving traffic. For more information about health check, see [https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210977_en-US.png)

        |Request method|Configuration description|
        |--------------|-------------------------|
        |HTTP request|An HTTP GET request is sent to the container. The following are supported parameters:        -   Protocol: HTTP/HTTPS
        -   Path: Path to access the HTTP server
        -   Port: Number or name of the port exposed by the container. The port number must be in the range of 1 to 65535.
        -   HTTP Header: Custom headers in the HTTP request. HTTP allows repeated headers. Supports key-value configuration.
        -   Initial Delay \(in seconds\): Namely, the **initialDelaySeconds**. Seconds for the first probe has to wait after the container is started. The default is 3.
        -   Period \(in seconds\): Namely, the **periodseconds**. Intervals at which the probe is performed. The default value is 10. The minimum value is 1.
        -   Timeout \(in seconds\): Namely, the **timeoutSeconds**. The time of probe timeout. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes that are considered as successful after a failed probe. The default is 1 and the minimum is 1. It must be 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes that are considered as failed after a successful probe. The default value is 3. The minimum value is 1.
|
        |TCP connection|A TCP socket is send to the container. The kubelet attempts to open a socket to your container on the specified port. If a connection can be established, the container is considered healthy. If not, it is considered as a failure. The following are supported parameters:        -   Port: Number or name of the port exposed by the container. The port number must be in the range of 1 to 65535.
        -   Initial Delay \(in seconds\): Namely, the **initialDelaySeconds**. Seconds for the first liveness or readiness probe has to wait after the container is started. The default is 15.
        -   Period \(in seconds\): Namely, the **periodseconds**. Intervals at which the probe is performed. The default value is 10. The minimum value is 1.
        -   Timeout \(in seconds\): Namely, the **timeoutSeconds**. The time of probe timeout. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes that are considered as successful after a failed probe. The default is 1 and the minimum is 1. It must be 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes that are considered as failed after a successful probe. The default value is 3. The minimum value is 1.
|
        |Command|Detect the health of the container by executing probe detection commands in the container. The following are supported parameters:        -   Command: A probe command used to detect the health of the container.
        -   Initial Delay \(in seconds\): Namely, the **initialDelaySeconds**. Seconds for the first liveness or readiness probe has to wait after the container is started. The default is 5.
        -   Period \(in seconds\): Namely, the **periodseconds**. Intervals at which the probe is performed. The default value is 10. The minimum value 1.
        -   Timeout \(in seconds\): Namely, the **timeoutSeconds**. The time of probe timeout. The default value is 1 and the minimum value is 1.
        -   Success Threshold: The minimum number of consecutive successful probes that are considered as successful after a failed probe. The default is 1 and the minimum is 1. It must be 1 for a liveness probe.
        -   Failure Threshold: The minimum number of consecutive failed probes that are considered as failed after a successful probe. The default value is 3. The minimum value is 1.
 |

5.  Click **Next** after completing the configurations. 
6.  Configure advanced settings. 
    1.  Configure **Access Control**. You can configure how to expose the backend pod and click **Create**. In this example, select Cluster IP Service and Ingress to create an nginx application that is accessible for Internet.

        **Note:** 

        To meet communication requirements of the application, you can configure access control based on your needs:

        -   Internal applications: For applications that work only inside a cluster, you can create services of Cluster IP or Node Port for internal communication as needed.
        -   External applications: For applications that need to be exposed to Internet, you can configure access control by using one of the following methods:
            -   Create a service of Server Load Balancer: Use the Server Load Balancer \(SLB\) service provided by Alibaba Cloud, which provides Internet accessibility for the application.
            -   Create a service of ClusterIP or NodePort, and create Ingress: This method provides Internet accessibility through ingress. For more information, see [https://kubernetes.io/docs/concepts/services-networking/ingress/](https://kubernetes.io/docs/concepts/services-networking/ingress/).
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210979_en-US.png)

        1.  Click **Create** at right of Service. Configure a service in the displayed dialog box, and then click **Create**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210980_en-US.png)

            -   **Name**: You can enter your custom name. The default is `applicationname-svc`.
            -   **Type**: Select one from the following three service types.
                -   ClusterIP: Expose the service by using the internal IP address of your cluster. With this type selected, the service is accessible only within the cluster.
                -   NodePort: Expose the service by using the IP address and static port \(NodePort\) on each node. A NodePort service routes to a ClusterIP service, which is automatically created. You can access the NodePort service outside the cluster by requesting`<NodeIP>:<NodePort>`.
                -   Server Load Balancer: The Server Load Balancer service, which is provided by Alibaba Cloud. You can configure Internet access or intranet access by using this type of service. Server Load Balancer can route to the NodePort service and ClusterIP service.
            -   **Port Mapping**: Add a service port and a container port. If you select NodePort for **Type**, you must configure a node port to avoid port conflicts. TCP and UDP protocols are supported.
            -   **annotation**: Add an annotation to the service. Server Load Balancer configuration parameters are supported, see [Access services by using Server Load Balancer](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress/Access services by using Server Load Balancer.md#).
            -   **Label**: You can add a label to the service to identify the service.
        2.  Click **Create** at the right of Ingress. Configure rout rules for the backend pod in the displayed dialog box, and then click **Create**. For more information about route configuration, see [Ingress configurations](reseller.en-US/User Guide/Kubernetes cluster/Server Load Balancer and Ingress/Ingress configurations.md#).

            **Note:** When you create an application by using an image, you can create ingress for only one service. In this example, use a virtual host name as the testing domain name. You need to add a record to the hosts. In actual work scenarios, use a filing domain name.

            ```
            101.37.224.146   foo.bar.com    #the IP address of ingress
            ```

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210981_en-US.png)

        3.  The created service and ingress are displayed in the access control section. You can reconfigure the service and ingress by clicking **Update** and **Delete**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210982_en-US.png)

    2.  Configure **Horizontal Pod Autoscaling \(HPA\)**. 

        You can choose whether to enable **HPA**. To meet the demands of applications under different loads, Container Service supports the container auto scaling, which automatically adjusts the number of containers according to the container CPU and memory usage.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010210978_en-US.png)

        **Note:** To enable auto scaling, you must configure required resources for the deployment. Otherwise, the container auto scaling cannot take effect. See the basic configuration of containers.

        -   **Metric**: CPU and memory. Configure a resource type as needed.
        -   **Condition**: The percentage value of resource usage. The container begins to expand when the resource usage exceeds this value.
        -   **Maximum Replicas**: The maximum number of replicas that the deployment can expand to.
        -   **Minimum Replicas**: The minimum number of replicas that the deployment can contract to.
    3.  Configure **Scheduling Affinity**. 

        You can configure node affinity, pod affinity, and pod anti affinity. For more information, see [https://kubernetes.io/docs/concepts/configuration/assign-pod-node/\#affinity-and-anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).

        **Note:** Affinity scheduling depends on node tags and pod tags. You can use built-in tads to schedule as well as configure tags for nodes and pods in advance.

        1.  Set **Node Affinity** by configuring node tags.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010211137_en-US.png)

            Node scheduling supports both required and preferred rules, and various operators such as In, NotIn, Exists, DoesNotExist, GT, and LT.

            -   **Required** rules must be satisfied and correspond to requiredDuringSchedulingIgnoredDuringExecution. The required rules have the same effect as `NodeSelector`. In this example, the pod can be scheduled to only nodes with corresponding tags. You can add multiple required rules, but you only need to meet one of them.
            -   **Preferred** rules are not necessary satisfied and correspond to preferredDuringSchedulingIgnoredDuringExecution. In this example, the schedule tries not to schedule the pod to the node with the corresponding tag. You can also set weights for preferred rules. If multiple nodes that match the criteria exist, the node with the highest weight is scheduled as a priority. You can define multiple preferred rules, but all rules must be satisfied before scheduling.
        2.  Configure **Pod Affinity** to deploy the pod of the application in a topology domain together with other pods. For example, services that communicate with each other can be deployed to the same topology domain \(such as a host\) by configuring pod affinity scheduling to reduce network latency between them.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010211222_en-US.png)

            Schedule pods according to tags of pods running on nodes. Available expressions are `In, NotIn, Exists, DoesNotExist`.

            -   **Required** rules must be satisfied and correspond to requiredDuringSchedulingIgnoredDuringExecution. The pod affinity scheduling must meet configured rules.
                -   **Namespace**: The scheduling policy is based on pod tags so it is constrained by namespaces.
                -   **Topology Key**: Specifies the domain to be scheduled through tags of nodes. For example, if you set `kubernetes.io/hostname` as the topology key, nodes are used to identify topologies. If you specify`beta.kubernetes.io/os` as the topology key, operating systems of nodes are used to identify topologies.
                -   **Selector**: By clicking the Add button at the right of Selector, you can add hard constraint rules.
                -   **View Applicaiton List**: Click **View Applicaiton List**, a dialog box is displayed. In the dialog box, you can view applications in each namespace and export application tags to this affinity configuration dialog box.
                -   Hard constraints: Configure tags of existing applications, operators, and tag values. In this example, schedule the application to be created to this host that runs applications with the `app: nginx` tag.
            -   **Preferred** rules, that is, soft constraints, corresponding to preferredDuringSchedulingIgnoredDuringExecution. The pod affinity scheduling meet configured rules as soon as possible. For soft constraint rules, you can configure the weight of each rule. Other configuration requirements are the same as hard constraint rules.

                **Note:** **Weight**: Specifies the weight of one soft constraint rule in the range of 1 to 100. Weights of nodes that satisfies configured soft constraint rules are calculated through algorithm and then the pod is scheduled to the node with the greatest weight.

        3.  Configure **Pod Anti Affinity** to deploy the pod of the application in a topology domain excluding other pods. Scenarios that use pod anti affinity scheduling include:

            -   Distribute pods of a service to different topology domains \(such as hosts\) to improve the stability of the service.
            -   Grant a pot the exclusive access to a node so as to guarantee no other pods use resources of the node.
            -   Distribute pods of services that may affect each other to different hosts.
            **Note:** Configuration methods of pod anti affinity scheduling are the same as that of pod affinity. But the same scheduling rules have different meanings for pod anti affinity scheduling. Select an appropriate scheduling rule based on scenarios.

7.  Click **Create**. 
8.  After you create the application, the create success page is displayed and objects contained in the application are listed by default. You can click **View detail** to view the deployment details. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010310983_en-US.png)

    The nginx-deployment page is displayed by default.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010310984_en-US.png)

9.  Click **Application** \> **Ingress** in the left-side navigation pane, a rule is displayed under the Ingress list. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010310985_en-US.png)

10. Access the Ingress testing domain in a browser and you can see that the Nginx welcome page is displayed. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/153923010310986_en-US.png)



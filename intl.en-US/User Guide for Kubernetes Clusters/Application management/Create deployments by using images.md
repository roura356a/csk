# Create deployments by using images {#task_p2s_2rl_vdb .task}

You can use an image to create an Nginx application that is accessible to the Internet.

Create a Kubernetes cluster. For more information, see [EN-US\_TP\_16639.md\#](reseller.en-US//Create Kubernetes clusters.md#).

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, choose **Applications** \> **Deployments** and then click **Create from Image** in the upper-right corner.
3.  Set the following parameters: **Name**, **Cluster**, **Namespace**, **Replicas**, **Type**, **Annotations**, and **Labels**. The replicas parameter specifies the number of Pods. Click **Next**. 

    **Note:** In this example, set the type parameter to **Deployment**.

    If you do not set the **Namespace** parameter, the default namespace is used.

    ![General settings](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403810973_en-US.png)

4.  Configure containers. 

    **Note:** You can configure multiple containers to run in the Pods.

    1.  Container general settings. 

        -   **Image Name**: You can click **Select Image** to select the image in the dialog box that appears. In this example, select Nginx and click **OK**.

            You can also enter a private registry to specify the image. The format is as follows: `domainname/namespace/imagename:tag`.

        -   **Image Version**: You can click **Select Image Version** to select the version. If you do not specify the image version, the latest version is used by default.
        -   **Always Pull Image**: To improve efficiency, Container Service caches the image. During deployment, if the version of the newly specified image is the same as that of the cached image, Container Service will reuse the cached image rather than pull the image again. Therefore, if the image version is kept unchanged for some reason, for example, to make it easy to run upper-layer services, when the code or image is updated, the previously cached image will be used during deployment. When this option is selected, Container Service will always re-pull the image from the repository to deploy the application. This ensures that the latest image and code are used.
        -   **Set Image Secret**: Click **Set Image Secret** to set the image secret. You must set the secret if you need to access a private repository.[Use an image Secret](reseller.en-US/User Guide for Kubernetes Clusters/Application management/Use an image Secret.md#)
        -   **Resource Limit**: The upper limits of CPU and memory resources that can be used by this application. This prevents the application from using excessive resources. The unit of CPU resources is Core. The unit of memory is MiB.
        -   **Required Resources**: The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from being unavailable when other services or processes compete for resources.
        -   **Init Container**: When this option is selected, the system creates an Init Container that contains useful tools. For more information, see [https://kubernetes.io/docs/concepts/workloads/pods/init-containers/](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/).
        ![General configuration](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403810974_en-US.png)

    2.  Configure environment variables. 

        You can configure environment variables for the Pods by using key-value pairs. Environment variables are used to add environment labels or pass configurations to the Pods. For more information, see [Pod variable](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    3.  Configure **Health Check** settings. 

        Health check settings include liveness and readiness probes. Liveness probes are used to detect when to restart the container. Readiness probes determine if the container is ready to start accepting traffic. For more information about health check, see [https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes).

        ![Health Check](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403810977_en-US.png)

        |Request type|Description|
        |------------|-----------|
        |HTTP request|Sends an HTTP GET request to the container. Supported parameters are as follows:         -   Protocol: HTTP or HTTPS
        -   Path: The requested path on the server.
        -   Port: The port exposed by the container. The port number must be in the range of 1 to 65535.
        -   HTTP Header: The custom headers in the HTTP request. Replicate headers are allowed. Supports key-value pairs.
        -   Initial Delay \(s\): The initialDelaySeconds field. The time \(in seconds\) to wait before performing the first probe after the container is started. Default is 3.
        -   Period \(s\): The periodSeconds field. How often \(in seconds\) to perform the probe. Default is 10. Minimum is 1.
        -   Timeout \(s\): The timeoutSeconds. The time \(in seconds\) after which the probe times out. Default is 1. Minimum is 1.
        -   Healthy Threshold: The minimum number of consecutive successes that must occur for the probe to be considered successful after having failed. Default is 1. Minimum is 1. For liveness probes, this parameter must be set to 1.
        -   Failure Threshold: The minimum number of consecutive failed probes that are considered as failed after a successful probe. Default is 3. Minimum is 1.
 |
        |TCP connection|Sends a TCP socket to the container. The kubelet will attempt to open a socket to your container on the specified port. If a connection can be established, the container is considered healthy. Otherwise, it is considered unhealthy. Supported parameters are as follows:         -   Port: The port exposed by the container. The port number must be in the range of 1 to 65535.
        -   Initial Delay \(s\): The initialDelaySeconds field. The time \(in seconds\) to wait before performing the first probe after the container is started. Default is 15.
        -   Period \(s\): The periodSeconds field. How often \(in seconds\) to perform the probe. Default is 10. Minimum is 1.
        -   Timeout \(s\): The timeoutSeconds. The time \(in seconds\) after which the probe times out. Default is 1. Minimum is 1.
        -   Healthy Threshold: The minimum number of consecutive successes that must occur for the probe to be considered successful after having failed. Default is 1. Minimum is 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: The minimum number of consecutive failures that must occur for the probe to be considered failed after having succeeded. Default is 3. Minimum is 1.
 |
        |Command line|Runs a probe command in the container to check its health. Supported parameters are as follows:         -   Command: The probe command that is used to check the health of the container.
        -   Initial Delay \(s\): The initialDelaySeconds field. The time \(in seconds\) to wait before performing the first probe after the container is started. Default is 5.
        -   Period \(s\): The periodSeconds field. How often \(in seconds\) to perform the probe. Default is 10. Minimum is 1.
        -   Timeout \(s\): The timeoutSeconds. The time \(in seconds\) after which the probe times out. Default is 1. Minimum is 1.
        -   Healthy Threshold: The minimum number of consecutive successes that must occur for the probe to be considered successful after having failed. Default is 1. Minimum is 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: The minimum number of consecutive failures that must occur for the probe to be considered failed after having succeeded. Default is 3. Minimum is 1.
 |

    4.  Configure lifecycle events. 

        You can set the following parameters to configure the lifecycle of the container: start, postStart, and preStop. For more information, see [https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Start**: The pre-start command and parameter.
        -   **Post Start**: The post-start command.
        -   **Pre Stop**: The pre-stop command.
        ![Configure lifecycle](http://icms-static-translation.oss-cn-hangzhou.aliyuncs.com/SP_235/DNcsk1877477/images/11371_zh-CN.png?Expires=1566782388&OSSAccessKeyId=LTAIJfoPL6wmrirR&Signature=mZEgsDirJLYJ8uudpwV7S%2BdzSV8%3D)

    5.  Configure volumes. 

        Local volumes and cloud volumes are supported.

        -   **Local Volume**: Supports hostPath, ConfigMaps, Secrets, and temporary directories. Local volumes mount the corresponding mount source to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **Cloud Volume**: Supports three types of PVs: cloud disks, NAS, and OSS.
        This example selects a PV created from a cloud disk and mounts the PV to the /tmp path in the container. Data generated in this path is stored to the cloud disk.

        ![Configure volumes](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403810976_en-US.png)

    6.  Configure **Log Service**. You can configure collection methods and custom labels. 

        **Note:** Make sure that the Log Service agent has been installed on the cluster.

        Configure log collection methods as follows:

        -   **Logstore**: Create a logstore to store log data in Log Service.
        -   **Log Path**: Supports stdout and text logs.
            -   **stdout**: Collects the container's standard output.
            -   **Text Logs**: Collects logs in the specific path of the container. This example collects text logs in the following path: /var/log/nginx. Wildcards are supported.
        You can also add custom labels to logs. Once added, the labels will be collected and output along with logs. Custom labels make it easy to perform statistical analysis on log data.

        ![Configure log collection](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403811364_en-US.png)

5.  Set other parameters based on your needs and then click **Next**.
6.  Configure advanced settings. 
    1.  Configure **Access Control** settings. You can configure how to expose the Pods and click **Create**. This example creates a Service of the Cluster IP type and an Ingress to build an Nginx application that is accessible to the Internet.

        **Note:** 

        You can configure access control based on your needs:

        -   Internal applications: For applications that run inside the cluster, you can create Services of the Cluster IP or Node Port type to enable internal communication as needed.
        -   External applications: For applications that need to be exposed to the Internet, you can configure access control by using one of the following methods:
            -   Create a Service of the Server Load Balancer type and expose your application to the Internet through the SLB instance.
            -   Create a Service of the Cluster IP or Node Port type, create an Ingress, and expose your application to the Internet through the Ingress. For more information, see [https://kubernetes.io/docs/concepts/services-networking/ingress/](https://kubernetes.io/docs/concepts/services-networking/ingress/).
        ![Create application 1](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403910979_en-US.png)

        1.  To create a Service, click **Create** in the Access Control section. Configure the Service in the dialog box that appears, and then click **Create**.

            ![Create application 2](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403910980_en-US.png)

            -   **Name**: The service name. Default is `applicationname-svc`.
            -   **Type**: Select one from the following three types.
                -   Cluster IP: Expose the Service through an internal IP address in the cluster. When selected, the Service is only accessible within the cluster.
                -   Node Port: Expose the Service through the IP address and static port \(NodePort\) on each node. The Node Port Service can route requests to a Cluster IP Service, which is automatically created by the system. You can access a Node Port Service from outside the cluster by requesting `<NodeIP>:<NodePort>`.
                -   Server Load Balancer: Expose the Service through Server Load Balancer, which supports Internet access and internal access. Server Load Balancer can route requests to Node Port and Cluster IP Services.
            -   **Port Mapping**: Set a service port and a container port. If the **Type** parameter is set to Node Port, you must set a node port to avoid port conflicts. TCP and UDP protocols are supported.
            -   **Annotations**: Add annotations to the Service. SLB parameters are supported. For more information, see [EN-US\_TP\_16677.md\#](reseller.en-US/User Guide for Kubernetes Clusters/Network management/Access services by using SLB.md#).
            -   **Labels**: Add labels to the Service.
        2.  To create an Ingress, click **Create** in the Access Control section. Configure Ingress rules in the dialog box that appears, and then click **Create**. For more information about Ingress configuration, see [EN-US\_TP\_16681.md\#](reseller.en-US/User Guide for Kubernetes Clusters/Network management/Ingress configurations.md#).

            **Note:** When you create an application by using an image, you can create an Ingress for one Service only. This example uses a virtual host name as the test domain. You need to add a record to the hosts file. In actual scenarios, use a domain that has obtained an ICP filing.

            ``` {#codeblock_unx_40k_cnw}
            101.37.224.146   foo.bar.com    #The IP address of the Ingress
            ```

            ![Configure routing rules](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403910981_en-US.png)

        3.  You can find the newly created Service and Ingress in the Access Control section. You can click **Update** or **Delete** to make changes.

            ![Change and delete routing rules](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403910982_en-US.png)

    2.  Configure **Horizontal Pod Autoscaling \(HPA\)**. 

        You can enable **HPA** to automatically scale the number of Pods based on the CPU and memory utilization. This enables the application to run smoothly at different load levels.

        ![Configure HPA](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403910978_en-US.png)

        **Note:** To enable HPA, you must configure resource objects that can be scaled for the container. Otherwise, HPA would not work. For more information, see the general settings section.

        -   **Metric**: Supports CPU and memory. The resource type must be the same as the one you have specified in the Required Resources field.
        -   **Condition**: Specify the resource usage threshold. HPA starts scaling up the cluster when the threshold is exceeded.
        -   **Maximum Replicas**: The maximum number of replicas that the deployment can expand to.
        -   **Minimum Replicas**: The minimum number of replicas that the deployment keeps running.
    3.  Configure **Scheduling** settings. 

        You can set the following parameters: update method, node affinity, pod affinity, and pod anti affinity. For more information, see [https://kubernetes.io/docs/concepts/configuration/assign-pod-node/\#affinity-and-anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity).

        **Note:** The affinity feature facilitates scheduling based on node labels and Pod labels. You can use built-in labels or add custom labels based on needs.

        1.  Set the **Update Method**.

            You can select from RollingUpdate and Recreate. For more information, see [https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/).

        2.  Set **Node Affinity** rules.

            ![Set node affinity](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403911137_en-US.png)

            Node scheduling supports required and preferred rules, and various operators such as In, NotIn, Exists, DoesNotExist, Gt, and Lt.

            -   **Required** rules must be met and are specified in the requiredDuringSchedulingIgnoredDuringExecution field of nodeAffinity. These rules have the same effect as `NodeSelector`. In this example, Pods can only be scheduled to nodes with specific labels. You can create multiple required rules in a way that only one of them must be met.
            -   **Preferred** rules may not be met and are specified in the preferredDuringSchedulingIgnoredDuringExecution field of nodeAffinity. In this example, the scheduler tries not to schedule Pods to the nodes with specific labels. You can also set weights for preferred rules. If multiple nodes match the rule, the node with the highest weight is preferred. You can create multiple preferred rules in a way that all of them must be met before scheduling the Pods.
        3.  Set **Pod Affinity** rules. These rules specify how Pods are deployed relative to other Pods in the same topology domain. For example, you can use Pod affinity rules to deploy services that communicate with each other to the same topology domain, such as a host. This helps reduce the network latency between these services.

            ![Configure Pod affinity](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403911222_en-US.png)

            Pod affinity enables you to specify which nodes the Pods can be scheduled to based on the labels on other Pods. This feature supports required and preferred rules, and the following operators: `In, NotIn, Exists, DoesNotExist`.

            -   **Required** rules must be met and are specified in the requiredDuringSchedulingIgnoredDuringExecution field of podAffinity. Required rules must be met before a Pod can be scheduled to a node.
                -   **Namespace**: The rule is defined based on the labels on Pods and therefore must be divided into namespaces.
                -   **Topology Domain**: Specify the topologyKey, which is the key of the node label that the system uses to denote the topology domain. For example, if you set the parameter to `kubernetes.io/hostname`, nodes are used to identify topologies. If set to `beta.kubernetes.io/os`, the operating systems of nodes are used to identify topologies.
                -   **Selector**: Click Add to add multiple required rules.
                -   **View Applicaitons**: Click **View Applicaitons** and specify the namespace and application in the dialog box that appears. You can view the labels on the selected application and add them to the rule configuration page.
                -   Required rule: Specify the label on the existing application, the operator, and the label value. This example schedules the application to be created to the host that runs the application with label `app: nginx`.
            -   **Preferred** rules may not be met and are specified in the preferredDuringSchedulingIgnoredDuringExecution field of podAffinity. A preferred rule specifies that, if the rule is met, the scheduler tries to enforce the rule. You can set weights for preferred rules. The other parameters are the same as those of required rules.

                **Note:** **Weight**: Set the weight for a preferred rule to a value between 1 to 100. The scheduler calculates a weight for each node that meets the rule based on an algorithm and schedules the Pod to the node with the highest weight.

        4.  Set **Pod Anti Affinity** rules to prevent scheduling Pods to the same topology where other Pods with specific labels are already deployed. Pod anti affinity rules can be used in the following scenarios:

            -   Schedule the Pods of a service to different topology domains, such as multiple hosts, to enhance the stability of the service.
            -   Grant a Pod exclusive access to a node. This ensures resource isolation and guarantees that no other Pod can share the specified node.
            -   Schedule the Pods of a service to different hosts if these Pods may interfere each other.
            **Note:** The parameters of pod anti affinity rules are the same as those of pod affinity rules. Create the rules based on different scenarios.

7.  Click **Create**.
8.  After the application is created, you are redirected to the Complete page. You can find the resource objects under the application and click **View Details** to view application details. 

    ![View details](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403910983_en-US.png)

    The nginx-deployment details page appears.

    ![View details 2](images/51759_en-US.png)

    **Note:** You can also create Ingresses and Services as follows: In the above figure, click the **Access Method** tab.

    -   Click **Create** next to Service. Follow the steps introduced in 6.i.a to create a Service.
    -   Click **Create** next to Ingress. Follow the steps introduced in 6.i.b to create an Ingress.
9.  In the left-side navigation pane, choose **Ingresses and Load Balancing** \> **Ingresses**. You can find the newly created Ingress on this page. 

    ![Routing rule](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403910985_en-US.png)

10. Visit the test domain through a browser and the Nginx welcome page appears. 

    ![Nginx welcome page](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/17653/156905403910986_en-US.png)



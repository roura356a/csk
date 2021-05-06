---
keyword: [orchestration template, image pull secret, commands, create a stateless application]
---

# Use a Deployment to create a stateless application

Container Service for Kubernetes \(ACK\) allows you to create a stateless application by using an image, an orchestration template, or kubectl commands. ACK allows you to use Secrets to pull images by using a web interface. This topic describes how to use images, orchestration templates, and kubectl commands to create stateless NGINX applications.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   The kubectl client is connected to the ACK cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).
-   A private image repository is created and your image is uploaded to the repository. An image repository from Alibaba Cloud Container Registry is used in this topic. For more information, see [Manage images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Manage images.md).

## Create a stateless application from an image

**Step 1: Configure basic settings**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure basic settings.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Replicas|The number of pods that are provisioned for the application. Default value: 2.|
    |Type|The type of application. You can select **Deployments**, **StatefulSets**, **Jobs**, ******Cron Jobs**, or **DaemonSets**.|
    |Label|Add a label to the application. Labels are used to identify the application.|
    |Annotations|Add an annotation to the application.|
    |Synchronize Timezone|Specify whether to synchronize the time zone between nodes and containers.|

    **Note:** **Deployments** is selected in this example.

7.  Click **Next**.

    The **Container** wizard page appears.


**Step 2: Configure containers**

On the **Container** wizard page, configure the following settings for containers: images, resources, ports, environment variables, health checks, lifecycle, volumes, and Log Service.

**Note:** In the upper part of the **Container** wizard page, click **Add Container** to add containers to the pods of the application.

1.  In the **General** section, configure basic settings of the container.

    |Parameter|Description|
    |---------|-----------|
    |Image Name|    -   To use a Docker image or an image from Container Registry, click **Select Image**. In the dialog box that appears, select an image and click **OK**. In this example, an NGINX image is selected. On the **Search** tab, select **Docker Images** from the drop-down list, enter NGINX in the search bar, and then click Search.
        -   Alibaba Cloud Container Registry: On the **Alibaba Cloud Container Registry** tab, you can select an image from Container Registry. Specify the region and the Container Registry instance to which the image belongs. For more information, see [What is Container Registry Enterprise Edition?]().

**Note:** On the **Alibaba Cloud Container Registry** tab, you can enter an image name to search for an image.

        -   Docker Official Images: On the **Docker Official Images** tab, you can select a Docker image.
        -   Favorite Images: On the **Favorite Images** tab, you can select a Docker image that you have added to your favorite list.
        -   Search: On the **Search** tab, you can select Alibaba Cloud Image from the drop-down list and specify a region to search for an image in Container . You can also select Docker Images from the drop-down list and search for a Docker image.
    -   You can also enter the address of a private registry. The registry address must be in the `domainname/namespace/imagename:tag` format. |
    |Image Version

|    -   Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.
    -   You can select the following image pull policies:

        -   **ifNotPresent**: If the image you want to pull is found in the region where the cluster is deployed, the local image is used. Otherwise, ACK pulls the image from the corresponding repository.
        -   **Always**: ACK pulls the image from the repository each time the application is deployed or expanded.
        -   **Never**: ACK uses only local images.
**Note:** If you select **Image Pull Policy**, no image pull policy is applied for the deployment of the application.

    -   To pull the image without a secret, click **Set Image Pull Secret** to set a secret for pulling images. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use aliyun-acr-credential-helper to pull images without a password.md). |
    |Resource Limit|You can specify upper limits for the CPU, memory, and ephemeral storage resources that the container can consume. This prevents the container from occupying an excess amount of resources.|
    |Required Resources|The amount of CPU and memory resources that are reserved for the container. These resources are exclusive to the container. This prevents the application from becoming unavailable when other services or processes occupy these resources.|
    |Container Start Parameter|    -   stdin: specifies that start parameters defined in the console are sent to the container.
    -   tty: specifies that start parameters defined in a virtual terminal are sent to the console. |
    |Privileged Container|    -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
    -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
    |Init Container|If you select Init Container, an init container is created. An init container contains useful utilities. For more information, see [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx).|

2.  In the **Ports** section, click **Add** to configure one or more ports for the container.

    -   Name: Enter a name for the container port.
    -   Container Port: Enter the container port that you want to open. Enter a port number from 1 to 65535.
    -   Protocol: Select TCP or UDP.
3.  In the **Environments** section, click **Add** to configure one or more environment variables.

    You can configure environment variables for pods in key-value pairs. Environment variables are used to apply pod configurations to containers. For more information, see [Pod variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    -   Type: Select the type of environment variable. You can select **Custom**, **ConfigMaps**, **Secret**, **Value/ValueFrom**, or **ResourceFieldRef**. If you select ConfigMaps or Secret as the type of environment variable, all values in the selected ConfigMaps or Secret are passed to the container environment variables. In this example, Secret is selected.

        Select **Secret** from the Type drop-down list and select a Secret from the **Value/ValueFrom** drop-down list. All values in the selected Secret are passed to the environment variable.

        ![Environment variable](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9007846161/p130410.png)

        In this case, the YAML file that is used to deploy the application contains the settings that reference all values in the specified Secret.

        ![yaml](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9007846161/p130413.jpg)

    -   Variable Key: Specify the key of the environment variable.
    -   Value/ValueFrom: Specify the value that is referenced by the environment variable.
4.  In the **Health Check** section, you can enable **liveness** and **readiness** probes based on your business requirements.

    For more information about health checks, see [Configure Liveness, Readiness, and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

    -   Liveness probes are used to determine when to restart the container.
    -   Readiness probes are used to determine whether the container is ready to accept traffic.
    |Request type|Description|
    |------------|-----------|
    |HTTP|Sends an HTTP GET request to the container. You can configure the following parameters:    -   Protocol: HTTP or HTTPS.
    -   Path: the requested path on the server.
    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. You can specify the HTTP headers in key-value pairs.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 3.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the time interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successes that must occur before a container is considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of consecutive failures that must occur before a container is considered unhealthy after a success. Default value: 3. Minimum value: 1. |
    |TCP|Sends a TCP socket to the container. Kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. You can configure the following parameters:    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 15.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the time interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successes that must occur before a container is considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of consecutive failures that must occur before a container is considered unhealthy after a success. Default value: 3. Minimum value: 1. |
    |Command|Runs a probe command in the container to check the health status of the container. You can configure the following parameters:    -   Command: the probe command that is run to check the health status of the container.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 5.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the time interval \(in seconds\) at which probles are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successes that must occur before a container is considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of consecutive failures that must occur before a container is considered unhealthy after a success. Default value: 3. Minimum value: 1. |

5.  In the **Lifecycle** section, configure the lifecycle of the container.

    You can configure the Start, Post Start, and Pre Stop parameters to specify the lifecycle of the container. For more information, see [Attach Handlers to Container Lifecycle Events](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

    -   **Start**: Set the command and parameter that take effect before the container starts.
    -   **Post Start**: Set the command that takes effect after the container starts.
    -   **Pre Stop**: Set the command that takes effect before the container stops.
    ![Lifecycle](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6705677161/p134220.png)

6.  In the **Volume** section, you can mount on-premises volumes and persistent volume claims \(PVCs\) to the container.

    -   Add Local Storage: You can select HostPath, ConfigMap, Secret, or EmptyDir. The specified volume is mounted to a path in the container. For more information, see [volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   Add PVC: You can select Cloud Storage.
    In this example, a PVC named disk-ssd is mounted to the /tmp path of the container.

    ![Configure a volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8045359951/p12307.png)

7.  In the **Log** section, you can specify collection configurations and add custom tags.

    **Note:** Make sure that the Log Service agent is installed in the cluster.

    |Parameter|Description|
    |---------|-----------|
    |Collection Configuration|Logstore: Create a Logstore in Log Service to store log data.|
    |Log Path in Container: Specify stdout or a path to collect log data.

    -   stdout: specifies that the stdout files are collected.
    -   Text Logs: specifies that logs in the specified path of the container are collected. In this example, /var/log/nginx is specified as the path. Wildcard characters can be used in the path. |
    |Custom Tag|You can also add custom tags. The tags are added to the logs of the container when the logs are collected. Log data with tags is easier to aggregate and filter.|

8.  Click **Next**.

    The **Advanced** wizard page appears.


**Step 3: Configure advanced settings**

On the **Advanced** wizard page, configure the following settings: access control, scaling, scheduling, annotations, and labels.

1.  In the **Access Control** section, you can configure access control settings to expose backend pods.

    **Note:**

    You can configure the following access control settings based on your business requirements:

    -   Internal applications: For applications that run inside the cluster, you can create a **ClusterIP** or **NodePort** Service to enable internal communication.
    -   External applications: For applications that are open to the Internet, you can configure access control by using one of the following methods:
        -   Create a LoadBalancer Service. When you create a Service, set the Type parameter to **Server Load Balancer**. You can select or create a Server Load Balancer \(SLB\) instance for the Service and use the Service to expose your application to the Internet.
        -   Create an Ingress and use it to expose your application to the Internet. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
    You can also specify how the backend pods are exposed to the Internet. In this example, a ClusterIP Service and an Ingress are created to expose the NGINX application to the Internet.

    -   To create a Service, click **Create** on the right side of **Services**. In the Create Service dialog box, configure the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Parameter|The name of the Service. In this example, nginx-svc is used.|
        |Type|The type of Service. This parameter determines how the Service is accessed. **Cluster IP** is selected in this example.         -   **Cluster IP**: exposes the Service by using an internal IP address of the cluster. If you select this option, the Service is accessible only from within the cluster. This is the default value.

**Note:** The **Headless Service** checkbox is available only when you set Type to **Cluster IP**.

        -   **Node Port**: exposes the Service by using the IP address and static port of each node. The NodePort field specifies the static port. A NodePort Service can be used to route requests to a ClusterIP Service. The ClusterIP Service is automatically created by the system. You can access a NodePort Service from outside the cluster by sending requests to `<NodeIP>:<NodePort>`.
        -   **Server Load Balancer**: exposes the Service by using an SLB instance. If you select this option, you can enable internal or external access to the Service. SLB instances can be used to route requests to NodePort and ClusterIP Services.

            -   Create SLB Instance: You can click **Modify** to change the specification of the SLB instance.
            -   Use Existing SLB Instance: You can select an existing SLB instance.
**Note:** You can create an SLB instance or use an existing SLB instance. You can also associate an SLB instance with more than one Service. However, you must take note of the following limits:

            -   If you use an existing SLB instance, the listeners of the SLB instance overwrite the listeners of the Service.
            -   If an SLB instance is created for a Service, you cannot reuse the SLB instance to expose other Services. Otherwise, the SLB instance may be accidentally deleted. Only SLB instances that are created in the console or by calling API operations can be reused.
            -   An SLB instance must listen on different Service ports if the SLB instance exposes more than one Service. Otherwise, port conflicts may occur.
            -   When you use an SLB instance, the names of listeners and vServer groups are used as unique identifiers in Kubernetes. Do not modify the names of listeners or vServer groups.
            -   You cannot use an SLB instance to expose Services across clusters. |
        |Port Mapping|Specify the Service port and container port. The container port must be the same as the one that is exposed in the backend pod.|
        |External Traffic Policy|        -   Local: routes network traffic only to the node where the Service is deployed.
        -   Cluster: routes network traffic to pods on other nodes.
**Note:** The **External Traffic Policy** parameter is available only when you set Type to **Node Port** or **Server Load Balancer**. |
        |Annotations|Add one or more annotations to the Service to modify the configurations of the SLB instance. For example, `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the maximum bandwidth of the Service is 20 Mbit/s. This limits the amount of traffic that flows through the Service. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md).|
        |Label|Add a label to the Service.|

    -   To create an Ingress, click **Create** on the right side of **Ingresses**. In the Create dialog box, configure the parameters.

        For more information about how to configure an Ingress, see [Basic operations of an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).

        **Note:** When you deploy an application from an image, you can create an Ingress for only one Service. In this example, the name of a virtual host is used as the test domain name. You must add the following entry to the hosts file to map the domain name to the IP address of the Ingress. The entry is in the format of <Ingress external endpoint\> + <Ingress domain name\>. In actual scenarios, use a domain name that has obtained an Internet Content Provider \(ICP\) number.

        ```
        101.37.xx.xx   foo.bar.com    # The IP address of the Ingress. 
        ```

        |Parameter|Description|
        |---------|-----------|
        |Name|The name of the Ingress. In this example, nginx-ingress is used.|
        |Rules|Ingress rules that are used to enable access to a specified Service in a cluster. For more information, see [Basic operations of an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).         -   **Domain**: Enter the domain name of the Ingress. In this example, the test domain name `foo.bar.com` is used.
        -   **Path**: Enter the Service URL. The default path is the root path /. The default path is used in this example. Each path is associated with a backend Service. SLB forwards traffic to a backend Service only when inbound requests match the domain name and path.
        -   **Services**: Select the Service and port. In this example, the nginx-svc Service is selected.
        -   Select **EnableTLS** to enable TLS. For more information, see [Advanced Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md). |
        |Weight|Set the weight for each Service in the path. Each weight is calculated as a percentage value. Default value: 100.|
        |Canary Release|After a canary release rule is configured, only requests that match the rule are routed to the new release of the Service. If the weight of a Service is less than 100%, requests that match the rule are routed to the Service based on the weight. ACK supports multiple traffic splitting methods, which are applicable to various scenarios, such as canary releases and A/B testing. These methods include:         -   Traffic splitting based on request headers
        -   Traffic splitting based on cookies
        -   Traffic splitting based on query parameters
**Note:** Only Ingress controllers of version 0.12.0-5 and later support traffic splitting.

The following parameters are required:        -   **Services**: the Service to be accessed.
        -   **Type**: the type of matching rule, such as Header, Cookie, and Query.
        -   **Name** and **Match Value**: user-defined request parameters that are specified in key-value pairs.
        -   **Matching Rule**: Regular expressions and exact matches are supported. |
        |Annotations|        -   Click **Rewrite Annotation** to add a rewrite annotation for the Ingress. For example, `nginx.ingress.kubernetes.io/rewrite-target:/` specifies that /path is redirected to the root path /. The root path can be recognized by the backend Service.
        -   You can also click **Add**, and enter the name and value of an annotation. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/). |
        |Labels|Add one or more labels to describe the features of the Ingress.|

    You can find the created Service and Ingress in the **Access Control** section. You can click **Update** or **Delete** to change the configurations.

2.  In the **Scaling** section, specify whether to enable **HPA** and **CronHPA**. This allows you to meet the resource requirements of the application at different load levels.

    ![scaling](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4104519061/p10978.png)

    -   HPA can automatically scale the number of pods in an ACK cluster based on the CPU and memory usage.

        **Note:** To enable HPA, you must configure required resources for the container. Otherwise, HPA does not take effect.

        |Parameter|Description|
        |---------|-----------|
        |Metric|Select CPU Usage or Memory Usage. The selected resource type must be the same as that specified in the Required Resources field.|
        |Condition|Specify the resource usage threshold. HPA triggers scaling activities when the threshold is exceeded.|
        |Max. Replicas|Specify the maximum number of pod replicas to which the application can be scaled.|
        |Min. Replicas|Specify the minimum number of pod replicas that must run.|

    -   CronHPA can scale an ACK cluster at a scheduled time. For more information about CronHPA, see [Create CronHPA jobs](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md).
3.  In the **Scheduling** section, you can configure the following parameters: Update Method, Node Affinity, Pod Affinity, Pod Anti Affinity, and Toleration. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/?spm=a2c4g.11186623.2.31.3fdd30dfnyevPx#affinity-and-anti-affinity).

    **Note:** Node affinity and pod affinity affect pod scheduling based on node labels and pod labels. You can add node labels and pod labels that are provided by Kubernetes to configure node affinity and pod affinity. You can also add custom labels to nodes and pods, and then configure node affinity and pod affinity based on these custom labels.

    |Parameter|Description|
    |---------|-----------|
    |Update Method|Select Rolling Update or OnDelete. For more information, see [Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/?spm=a2c4g.11186623.2.32.3fdd30dfnyevPx). |
    |Node Affinity|Add labels to worker nodes to set **Node Affinity**. Node affinity supports required and preferred rules, and various operators such as In, NotIn, Exists, DoesNotExist, Gt, and Lt.

    -   **Required**: Specify the rules that must be matched for a pod to be scheduled to a node. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. These rules have the same effect as the `NodeSelector` parameter. In this example, pods can be scheduled only to nodes with the specified labels. You can create multiple required rules. However, only one of them must be met.
    -   **Preferred**: Specify the rules that are not required to be matched for pod scheduling. Pods are scheduled to a node that matches the preferred rules when multiple nodes match the required rules. In the YAML file, these rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. In this example, the scheduler attempts to schedule a pod to a node that matches the preferred rules. You can set node weights in preferred rules. If multiple nodes match the required and preferred rules, the node with the highest weight is preferred for pod scheduling. You can create multiple preferred rules. However, all of them must be met before the pod can be scheduled. |
    |Pod Affinity|Pod affinity specifies that pods can be scheduled to nodes or topological domains where pods with matching labels are deployed. For example, you can use pod affinity to deploy services that communicate with each other to the same topological domain, such as a host. This reduces the network latency between these services. Pod affinity enables you to specify to which node pods can be scheduled based on the labels on other running pods. Pod affinity supports required and preferred rules, and the following operators: `In, NotIn, Exists, and DoesNotExist`.

    -   **Required**: Specify rules that must be matched for pod scheduling. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. A node must match the required rules before pods can be scheduled to the node.
        -   **Namespace**: Specify the namespace to apply the required rule. Pod affinity rules are defined based on the labels that are added to pods and therefore must be scoped to a namespace.
        -   **Topological Domain**: Set the topologyKey. This specifies the key for the node label that the system uses to denote the topological domain. For example, if you set the parameter to`kubernetes.io/hostname`, topologies are determined by nodes. If you set the parameter to `beta.kubernetes.io/os`, topologies are determined by the operating systems of nodes.
        -   **Selector**: Click Add to add pod labels.
        -   **View Applications**: Click **View Applications** and set the namespace and application in the dialog box that appears. You can view the pod labels on the selected application and add the labels as selectors.
        -   Required Rules: Specify labels of existing applications, the operator, and the label value. In this example, the required rule specifies that the application to be created is scheduled to a host that runs applications with the `app:nginx` label.
    -   **Preferred**: Specify rules that are not required to be matched for pod scheduling. In the YAML file, preferred rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. The scheduler attempts to schedule the pod to a node that matches the preferred rules. You can set node weights in preferred rules. Configure the other parameters as described in the preceding settings.

**Note:** **Weight**: Set the weight of a preferred rule to a value from 1 to 100. The scheduler calculates the weight of each node that meets the preferred rule based on an algorithm, and then schedules the pod to the node with the highest weight. |
    |Pod Anti Affinity|Pod anti-affinity rules specify that pods are not scheduled to topological domains where pods with matching labels are deployed. Pod anti-affinity rules apply to the following scenarios:

    -   Schedule the pods of an application to different topological domains, such as multiple hosts. This allows you to enhance the stability of the service.
    -   Grant a pod exclusive access to a node. This enables resource isolation and ensures that no other pod can share the resources of the specified node.
    -   Schedule pods of an application to different hosts if the pods may interfere with each other.
**Note:** The parameters of pod anti-affinity rules are the same as those of pod affinity rules. You can create the rules for different scenarios. |
    |Toleration|Configure toleration rules to allow pods to be scheduled to nodes with matching taints.|
    |Schedule to Virtual Nodes|Specify whether to schedule pods to virtual nodes. This option is unavailable if the cluster does not contain a virtual node.|

4.  In the **Labels and Annotations** section, click **Add** to configure labels and annotations for the pod.

    -   Pod Labels: Add a label to the pod.
    -   Pod Annotations: Add an annotation to the pod.
5.  Click **Create**.


**Step 4: Check the application**

On the **Complete** wizard page, you can view the created application.

1.  On the **Complete** wizard page, click **View Details** to view application details.

    ![View details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10983.png)

    The nginx-deployment details page appears.

    **Note:** You can also follow these steps to create Ingresses and Services: Go to the **Access Method** section, as shown in the preceding figure.

    -   Click **Create** on the right side of Services to create a Service.
    -   Click **Create** on the right side of Ingresses to create an Ingress.
2.  In the left-side navigation pane, choose **Services and Ingresses** \> **Ingresses**. On the Ingresses page, you can find the created Ingress.

    ![Ingress rule](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10985.png)

3.  Enter the test domain name in the address bar of your browser and press Enter. The NGINX welcome page appears.

    ![NGINX welcome page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10986.png)


**Related operations**

**View application details**

In the left-side navigation pane, click **Clusters**. On the Clusters page, click the name of the cluster where the application is deployed or click **Details** in the **Actions** column. In the left-side navigation pane, choose **Workloads** \> **Deployments**. On the Deployments page, click the name of the application that you want to manage or click **Details** in the **Actions** column. On the details page of the application, you can edit, scale, redeploy, and refresh the application. You can also view the YAML file of the application.

|Action|Description|
|------|-----------|
|**Edit**|On the details page of the application, click **Edit** in the upper-right corner of the page to modify the configurations of the application.|
|**Scale**|On the details page of the application, click **Scale** in the upper-right corner of the page to scale the application to a required number of pods.|
|**View in YAML**|On the details page of the application, click **View in YAML** to **Update** or **Download** the YAML file. You can also click **Save As** to save the file as a different name.|
|**Redeploy**|On the details page of the application, click **Redeploy** in the upper-right corner of the page to redeploy the application.|
|**Refresh**|On the details page of the application, click **Refresh** in the upper-right corner of the page to refresh the application details page.|

**Manage the application**

On the Deployments page, select the application and click **More** in the **Actions** column to perform the following operations.

|Action|Description|
|------|-----------|
|**View in YAML**|View the YAML file of the application.|
|**Redeploy**|Redeploy the application.|
|**Edit Label**|Configure labels for the application.|
|**Node Affinity**|Configure node affinity rules for the application. For more information, see [Scheduling](#step_csu_4sv_gz8)[Scheduling](#step_cev_rgj_vck).|
|**Scaling**|Configure scaling settings for the application. For more information, see [HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md) and [CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md).|
|**Toleration**|Configure toleration rules for the application. For more information, see [Scheduling](#step_csu_4sv_gz8).|
|**Upgrade Policy**|Configure the upgrade policy for the application.-   **Rolling Update**: Pods are updated in a rolling update fashion.
-   **OnDelete**: All existing pods are deleted before new pods are created. |
|**Clone**|Create a new application by using the same container settings as the current application.|
|**Roll Back**|Roll back the application to a previous version.|
|**Logs**|View log data of the application.|
|**Delete**|Delete the application.|

## Create a Linux application by using an orchestration template

In an orchestration template, you must define the resources that are required for running an application and use mechanisms such as label selectors to manage these resources.

This topic describes how to use an orchestration template to create an NGINX application that consists of a Deployment and a Service. The Deployment provisions pods for the application and the Service manages access to the pods at the backend.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, click **Create from YAML** in the upper-right corner.

6.  Configure the parameters and click **Create**.

    -   **Namespace**: Select the namespace to which the resource objects belong. The default namespace is default. Most resources are scoped to namespaces, except for underlying computing resources, such as nodes and PVs.
    -   **Sample Template**: ACK provides YAML templates of various resource types. This simplifies the deployment of resource objects. You can also create a custom template based on YAML syntax to describe the resource that you want to define.
    -   **Add Deployment**: This feature allows you to define a YAML template.
    -   **Use Existing Template**: You can import an existing template to the configuration page.
    Based on an orchestration template provided by ACK, the following sample template is used to create a Deployment of an NGINX application.

    **Note:** ACK supports YAML syntax. You can use the `---` symbol to separate multiple resource objects. This allows you to create multiple resource objects in a single template.

    ```
    apiVersion: apps/v1 
    kind: Deployment
    metadata:
        name: nginx-deployment-basic
        labels:
          app: nginx
    spec:
        replicas: 2
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - name: nginx
              image: nginx:1.7.9 # replace it with your exactly <image_name:tags>
              ports:
              - containerPort: 80
    
    ---
    
    apiVersion: v1     
    kind: Service
    metadata:
       name: my-service1        #TODO: to specify your service name
       labels:
         app: nginx
    spec:
       selector:
         app: nginx             #TODO: change label selector to match your backend pod
       ports:
       - protocol: TCP
         name: http
         port: 30080            #TODO: choose an unique port on each node to avoid port conflict
         targetPort: 80
       type: LoadBalancer        ##In this example, the type is changed from Nodeport to LoadBalancer. 
    ```

7.  Click **Create**. A message that indicates the deployment status appears.


## Manage applications by using commands

This topic describes how to use commands to create applications or view containers of an application.

**Create an application by using commands**

1.  In a CLI, run the following command to start a container. An NGINX web server is used in this example.

    ```
     kubectl run -it nginx --image=registry.aliyuncs.com/spacexnice/netdia:latest
    ```

2.  Run the following command to create a service entry for this container. The `--type=LoadBalancer` parameter in the command indicates that the system must create a Server Load Balancer \(SLB\) instance for the NGINX container.

    ```
    kubectl expose deployment nginx --port=80 --target-port=80 --type=LoadBalancer
    ```


**View containers by using commands**

In the CLI, run the `kubectl get pods` command to list all the running containers in the default namespace.

```
NAME                                   READY     STATUS    RESTARTS   AGE
nginx-2721357637-d****                 1/1       Running   1          9h
```

## Create an application by using an image pull secret

Container Service for Kubernetes \(ACK\) allows you to use image pull secrets in the ACK console. You can create an image pull secret or use an existing image pull secret.

When you create an application by using a private image, you must set a secret for the image to ensure the security of the image. In the ACK console, you can specify the authentication information of the private image repository as a secret of the docker-registry type. This secret applies to the specified Kubernetes cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** page, select the namespace and click **Create from Image** in the upper-right corner.

6.  On the **Basic Information** wizard page, configure the parameters. For more information, see [Create a stateless application from an image](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

7.  Configure containers.

    The following example describes how to configure an image pull secret. For more information about container configurations, see [Create an application by using an image pull secret](#section_frc_gez_jd5).

    1.  On the **Container** wizard page, enter the address of the private image in the **Image Name** field. The address must be in the following format: `domainname/namespace/imagename`.

        **Note:** Public images do not require secrets.

    2.  Enter the version of the image in the Image Version field.

        A Tomcat private image is used in this example.

        ![Ingress settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13583.png)

    3.  Click **Set Image Pull Secret**, and create a secret or select an existing secret in the dialog box that appears.

        -   You can select **New Secret**. If you select New Secret, configure the following parameters:

            |Parameter|Description|
            |---------|-----------|
            |**Name** \(regsecret\)|The key name of the secret. You can enter a custom name.|
            |**Repository Domain** \(docker-server\)|The address of the specified Docker registry. If you use an image repository in Alibaba Cloud Container Registry, the address of the image repository is automatically displayed.|
            |**Username** \(docker-username\)|The username of the Docker repository. If you use an image repository in Alibaba Cloud Container Registry, your Alibaba Cloud account name is used as the username.|
            |**Password** \(docker-password\)|The password that is used to log on to the Docker repository. If you use an image repository in Alibaba Cloud Container Registry, the password is the logon password of Container Registry.|
            |**Email** \(docker-email\)|The email address. This parameter is optional.|

            Click **OK**. The newly created secret appears on the page.

            ![Specify a secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13585.png)

        -   You can also select **Existing Secret**. You can use commands or YAML files to create image pull secrets. For more information, see [How do I use private images in Kubernetes clusters?]() and [Create an application from a private image repository](/intl.en-US/Quick Start/Advanced operations/Create an application from a private image repository.md).

            ![Existing secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13586.png)

8.  Configure other parameters based on your requirements. Then, click **Create**.

    For more information, see Step 3: Configure advanced settings.

9.  In the left-side navigation pane, click **Clusters**.

10. On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

11. In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

12. On the Deployments page, check the running status of the application.

    Verify that the Tomcat application runs as expected. This indicates that the Tomcat private image is pulled by using the image pull secret.


## References

-   [Use a StatefulSet to create a stateful application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a StatefulSet to create a stateful application.md)
-   [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md)


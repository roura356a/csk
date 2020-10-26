# Use an image to create a stateless application

You can use an image to create a stateless application. This topic describes how to use an image to create a stateless NGINX application in the Container Service for Kubernetes \(ACK\) console.

[Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md)

## Step 1: Configure basic settings

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the **Deployments** tab, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure basic settings.

    ![Basic settings](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7534749951/p10973.png)

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Namespace|The namespace in which the application is deployed. If you do not set this field, the system uses the default namespace.|
    |Replicas|The number of pods in an application. Default value: 2.|
    |Type|The type of the application. You can select **Deployments**, **StatefulSets**, **Jobs**, **Cron Jobs**, and **DaemonSets**.|
    |Label|Add one or more labels to the application.|
    |Annotations|Add one or more annotations to the application.|
    |Synchronize Timezone|Specify whether to synchronize the timezone from nodes to containers.|

    **Note:** In this example, **Deployments** is selected as the application type.

7.  Click **Next**.

    The **Container** wizard page appears.


## Step 2: Configure the containers

On the **Container** wizard page, configure the settings of the image, resources, ports, environment variables, health check, lifecycle, volumes, and Log Service.

**Note:** At the top of the **Container** wizard page, click **Add Container** to add more containers for the application.

1.  In the **General** section, configure general settings of the container.

    ![General settings](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7045359951/p12305.png)

    |Parameter|Description|
    |---------|-----------|
    |Image Name|    -   To use a Docker image or an image from Container Registry \(ACR\), click **Select Image**. In the dialog box that appears, select an image and click **OK**. In this example, the NGINX image is selected. In the Select Image dialog box, click the **Search** tab, select **Docker Images** from the drop-down list, enter nginx in the search bar, and then click Search.
        -   Select an image from Alibaba Cloud Container Registry \(ACR\): In the Select Image dialog box, click the **Alibaba Cloud Container Registry** tab and select an image. You must first select a region and an ACR instance. For more information about ACR, see [What is Container Registry Enterprise Edition?]().

**Note:** On the **Alibaba Cloud Container Registry** tab, you can search for an image from ACR by name.

        -   Select a Docker image: In the Select Image dialog box, click the **Docker Official Images** tab and select a Docker image.
        -   Select a favorite image: In the Select Image dialog box, click the **Favorite Images** tab and select a favorite image.
        -   Search for a Docker image or an image from ACR: In the Select Image dialog box, click the **Search** tab and search for a Docker image or an image in ACR within a specified region by name.
    -   To use a private image, enter the address of a private image registry. The registry address must be in the `domainname/namespace/imagename:tag` format. |
    |Image Version

|    -   Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.
    -   You can select the following image pull policies:

        -   **ifNotPresent**: If the image you want to pull is found in the region where the cluster is deployed, the local image is used. Otherwise, ACK pulls the image from the corresponding repository.
        -   **Always**: ACK pulls the image from the repository each time the application is deployed or expanded.
        -   **Never**: ACK uses only local images.
**Note:** If you select **Image Pull Policy**, no image pull policy is applied for the deployment of the application.

    -   To pull the image without a secret, click **Set Image Pull Secret** to set a secret for pulling images. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use aliyun-acr-credential-helper to pull images without a password.md). |
    |Resource Limit|You can specify an upper limit for the CPU, memory, and ephemeral storage space that the container can consume. This prevents the container from occupying unnecessary resources.|
    |Required Resources|The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other services or processes occupy these resources.|
    |Container Start Parameter|    -   stdin: specifies that start parameters defined in the console are imported to the Linux system.
    -   tty: specifies that start parameters defined in a virtual terminal are imported to the console. |
    |Privileged Container|    -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
    -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
    |Init Container|If you select Init Container, an init container is created. An init container provides tools to manage pods. For more information, see [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx).|

2.  In the **Ports** section, click **Add** to set one or more ports for the container.

    -   Name: Enter a name for a port.
    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   Protocol: Select TCP or UDP.
3.  In the **Environments** section, click **Add** to set one or more environment variables.

    You can use key-value pairs to set environment variables for pods. Environment variables are used to apply pod configurations to containers. For more information, see [Expose Pod Information to Containers Through Environment Variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    -   Type: the type of the environment variable. You can select **Custom**, **ConfigMaps**, **Secret**, **Value/ValueFrom**, or **ResourceFieldRef**. If you select ConfigMaps or Secret as the type of the environment variable, all values in the selected ConfigMap or Secret are passed to the container environment variables. In this example, Secret is selected.

        Select **Secret** from the Name drop-down list and select a Secret from the **Value/ValueFrom** drop-down list. All values in the selected Secret are passed to the environment variable.

        In this case, the YAML file to deploy the application contains the settings that reference all values in the specified Secret.

    -   Variable Key: Specify the key of the environment variable.
    -   Value/ValueFrom: Specify a resource as the reference to define the environment variable.
4.  In the **Health Check** section, you can enable liveness and readiness probes as needed.

    For more information about health checks, see [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

    -   Liveness probes determine when to restart the container.
    -   Readiness probes determine whether the container is ready to accept traffic.
    |Request type|Description|
    |------------|-----------|
    |HTTP request|Sends an HTTP GET request to the container. You can set the following parameters:    -   Protocol: HTTP or HTTPS.
    -   Path: the requested path on the server.
    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. Key-value pairs are supported.
    -   Initial Delay \(s\): the initialDelaySeconds field. This field specifies the wait time \(in seconds\) to before the first probe is performed after the container is started. Default value: 3.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |
    |TCP connection|Opens a TCP socket to the container. The kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. You can set the following parameters:    -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) to before the first probe is performed after the container is started. Default value: 15.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |
    |Command|Runs a probe command in the container to check the health status of the container. You can set the following parameters:    -   Command: the probe command that is run to check the health status of the container.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) to before the first probe is performed after the container is started. Default value: 5.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |

5.  In the **Lifecycle** section, set the lifecycle of the container.

    You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Attach Handlers to Container Lifecycle Events](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

    -   **Start**: Set the command and parameter that take effect before the container starts.
    -   **Post Start**: Set the command that takes effect after the container starts.
    -   **Pre Stop**: Set the command that takes effect before the container starts.
6.  In the **Volume** section, you can mount local storage volumes and persistent volume claims \(PVCs\) to the container.

    -   Local Storage: You can select HostPath, ConfigMap, Secret, and EmptyDir. The source directory or file is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   PVC: Select Cloud Storage.
    In this example, a PVC named disk-ssd is mounted to the /tmp path of the container.

    ![Configure volumes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8045359951/p12307.png)

7.  In the **Log Service** section, you can specify collection methods and custom tags.

    **Note:** Make sure that the Log Service agent is installed in the cluster.

    |Parameter|Description|
    |---------|-----------|
    |Collection configuration|Logstore: Create a Logstore in Log Service to store log data.|
    |Log Path: Specify stdout or a path to collect logs.

    -   stdout: specifies that the stdout files are collected.
    -   Text Logs: specifies that logs in the specified path of the container are collected. In this example, /var/log/nginx is specified as the path. Wildcard characters can be used to set the log path. |
    |Custom tags|You can also set custom tags. Custom tags are added to the logs of the container when the logs are collected. Custom tags simplify how you collect log statistics, filter logs, and analyze logs.|

8.  Click **Next**.

    Go to the **Advanced** wizard page.


## Step 3: Configure the advanced settings

On the **Advanced** wizard page, you can configure the settings of access control, scaling, scheduling, annotations, and labels.

1.  In the **Access Control** section, you can configure the access control settings to enable access to pods that run the application.

    **Note:**

    You can configure the following access control settings based on your business requirements:

    -   Internal applications: For applications that run inside the cluster, you can create a service of the **Cluster IP** or **Node Port** type to enable internal communication.
    -   External applications: For applications that are open to the Internet, you can use one of the following access control methods:
        -   Create a service of the **Server Load Balancer** type and enable access to your application over the Internet by using a Server Load Balancer \(SLB\) instance.
        -   Create an Ingress to route external access to a service inside a cluster. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
    Configure the access control settings to enable access to pods that run the application. In this example, a Cluster IP service and an Ingress are created to enable access to the NGINX application over the Internet.

    -   To create a service, click **Create** on the right side of **Service**. In the Create Service dialog box, set the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Name|The name of the service. In this example, nginx-svc is used.|
        |Type|Select one of the following types. In this example, **Cluster IP** is selected.        -   **Cluster IP**: enables access to the service by using an internal IP address in the cluster. When this option is selected, the service is accessible only within the cluster. This is the default service type.

**Note:** The **Headless Service** option is available only when you set the service type to **Cluster IP**.

        -   **Node Port**: enables access to the service by using the IP address and static port on each node. The Node Port field specifies the static port. A Node Port service can be used to route requests to a Cluster IP service. The system automatically creates the Cluster IP service. You can access a Node Port service from outside the cluster by requesting `<NodeIP>:<NodePort>`.
        -   **Server Load Balancer**: enables access to the service by using an SLB instance. You can enable access over the Internet or an internal network. An SLB service can route requests to Node Port and Cluster IP services.

            -   Create SLB Instance: You can click **Modify** to change the SLB instance type.
            -   Use Existing SLB Instance: You can select an existing SLB instance.
**Note:** You can create a new SLB instance or use an existing SLB instance. Multiple services can reuse the same SLB instance. However, the following limits apply:

            -   If you use an existing SLB instance, the existing listeners of the SLB instance will be overwritten.
            -   If an SLB instance is created when you create a service, you cannot reuse this SLB instance when you create other services. Otherwise, the SLB instance may be deleted. You can reuse only SLB instances that are manually created in the console or by calling API operations.
            -   Services that reuse the same SLB instance cannot have the same frontend listening port. Otherwise, port conflicts may occur.
            -   When you reuse an SLB instance, you must use the listener name and the VServer group name as unique identifiers in Kubernetes. Do not modify the listener name or VServer group name.
            -   You cannot share SLB instances across clusters or regions. |
        |Port Mapping|Set a service port and a container port. If the Type parameter is set to Node Port, you must set a node port to avoid port conflicts. TCP and UDP are supported.|
        |External Traffic Policy|        -   Local: routes network traffic only to local pods.
        -   Cluster: forwards network traffic to the pods on other nodes.
**Note:** The **External Traffic Policy** drop-down list is available only when you set Type to **Node Port** or **Server Load Balancer**. |
        |Annotations|Add one or more annotations to the service. SLB parameters are supported. For example, `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the maximum bandwidth of the service is set to 20 Mbit/s to control access to the service. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).|
        |Label|Add one or more labels to the service.|

    -   To configure an Ingress, click **Create** on the right side of **Ingress**.

        For more information about the parameters required to create an Ingress, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Ingress configurations.md).

        **Note:** When you deploy an application from an image, you can create an Ingress for only one service. In this example, a virtual host name is used as the test domain name. You must add the following entry to the hosts file to map the domain name to the IP address of the Ingress. The entry is in the format of <Ingress external endpoint\>+<Ingress domain name\>. In actual scenarios, use a domain name that has obtained an ICP number.

        ```
        101.37.xxx.xxx   foo.bar.com    # The IP address of the Ingress.
        ```

        |Parameter|Description|
        |---------|-----------|
        |Name|Enter a name for the Ingress. In this example, nginx-ingress is used.|
        |Rules|Ingress rules that are used to enable access to the specified service of the cluster. For more information, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Ingress configurations.md).        -   **Domain**: Enter the Ingress domain name. In this example, the test domain name `foo.bar.com` is used.
        -   **Path**: Enter the URL for access to the service. The default path is the root path /. In this example, the default path is used. Each path is associated with a backend service. Server Load Balancer \(SLB\) forwards traffic to a backend service only when inbound requests match the domain name and path.
        -   **Service**: Set the service name and port. In this example, nginx-svc is used.
        -   Select **EnableTLS** to enable TLS and configure secure routing. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Configure an Ingress.md). |
        |Weight|Set the weights of the services that are associated with the Ingress. Each weight is calculated as a relative value. Default value: 100.|
        |Canary Release|After a canary release is configured, only requests that match specified rules are routed to the required service. If the weight of the service is less than 100%, requests that match the specified rules are routed to this service based on the weight. ACK supports multiple traffic splitting methods. This allows you to select more suitable solutions for specific scenarios, such as canary releases and A/B testing, including:        -   Traffic splitting based on request headers
        -   Traffic splitting based on cookies
        -   Traffic splitting based on query parameters
**Note:** Only Ingress controllers of version 0.12.0-5 and later support traffic splitting.

The following parameters are required:        -   **Services**: the service to be accessed.
        -   **Type**: the type of the matching rule, such as Header, Cookie, or Query.
        -   **Name** and **Match Value**: custom request fields. The name and matching value comprise a key-value pair.
        -   **Matching Rule**: Regular expressions and exact matches are supported. |
        |Annotations|        -   Click **Rewrite Annotation** to add a rewrite annotation for the Ingress. For example, `nginx.ingress.kubernetes.io/rewrite-target: /` specifies that /path is redirected to the root path /. The root path can be recognized by the backend service.
        -   You can also click **Add** and enter a name and a value to act as a key-value pair for the annotation. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/). |
        |Labels|Add one or more labels to describe the features of the Ingress.|

    You can find the newly created Service and Ingress in the **Access Control** section. You can click **Update** or **Delete** to change the configuration.

    ![Update or delete the Ingress](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10982.png)

2.  In the **HPA** section, specify whether to enable **HPA** to automatically scale the number of pods based on the CPU and memory usage.

    This enables the application to run as expected at different load levels.

    ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10978.png)

    **Note:** To enable Horizontal Pod Autoscaler \(HPA\), you must configure resources that support scaling for the container. Otherwise, HPA does not take effect.

    |Parameter|Description|
    |---------|-----------|
    |Metric|Select CPU Usage or Memory Usage. The selected resource type must be the same as the one you specified in the Required Resources field.|
    |Condition|Specify the resource usage threshold. HPA triggers scaling events when the threshold is exceeded.|
    |Max. Replicas|Specify the maximum number of replicated pods to which the application can be scaled.|
    |Min. Replicas|Specify the minimum number of replicated pods that must run.|

3.  In the **Scheduling** section, you can set the following parameters: Update Method, Node Affinity, Pod Affinity, Pod Anti Affinity, and Toleration. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/?spm=a2c4g.11186623.2.31.3fdd30dfnyevPx#affinity-and-anti-affinity).

    **Note:** Node affinity and pod affinity affect pod scheduling based on node labels and pod labels. You can add node labels and pod labels that are provided by Kubernetes to configure node affinity and pod affinity. You can also add custom labels to nodes and pods, and then configure node affinity and pod affinity based on these custom labels.

    |Parameter|Description|
    |---------|-----------|
    |Update Method|Select Rolling Update or OnDelete. For more information, see [Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/?spm=a2c4g.11186623.2.32.3fdd30dfnyevPx). |
    |Node Affinity|Add labels on worker nodes to set the node affinity.****Node Affinity supports required and preferred rules, and various operators such as In, NotIn, Exists, DoesNotExist, Gt, and Lt.

    -   **Required**: Set node labels that must be matched for pod scheduling. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. These rules have the same effect as the `NodeSelector` parameter. In this example, pods can be scheduled to only nodes with the specified labels. You can create multiple required rules. However, only one of them must be met.
    -   **Preferred**: Specify the node weight and node labels that are not required to be matched for pod scheduling. Pods are scheduled to a node that matches the preferred rules when multiple nodes match the required rules. In the YAML file, these rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. In this example, the scheduler attempts to schedule the pod to a node that matches the preferred rules. You can set node weights in preferred rules. If multiple nodes match the required and preferred rules, the node with the highest weight is preferred for pod scheduling. You can create multiple preferred rules. However, all of them must be met before the pods can be scheduled. |
    |Pod Affinity|Pod affinity specifies that pods can be scheduled to nodes or topological domains where pods with matching labels are deployed. For example, you can use pod affinity to deploy services that communicate with each other to the same topology domain, such as a host. This reduces the network latency between these services.Pod affinity enables you to specify which node pods can be scheduled based on the labels on other running pods. Pod affinity supports required and preferred rules, and the following operators: `In, NotIn, Exists, and DoesNotExist`.

    -   **Required**: Specify rules that must be matched for pod scheduling. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. A node must match the required rules before pods can be scheduled to the node.
        -   **Namespace**: Specify the namespace to apply the required rule. Pod affinity rules are defined based on the labels that are added to pods and therefore must be scoped to a namespace.
        -   **Topological Domain**: Set the topologyKey. This specifies the key for the node label that the system uses to denote the topology domain. For example, if you set the parameter to `kubernetes.io/hostname`, topologies are determined by nodes. If you set the parameter to `beta.kubernetes.io/os`, topologies are determined by the operating systems of nodes.
        -   **Selector**: Click Add to add pod labels.
        -   **View Applications**: Click **View Applications** and set the namespace and application in the dialog box that appears. You can view the pod labels on the selected application and add the labels to as selectors.
        -   Required rules: Specify labels on existing applications, the operator, and the label value. In this example, the required rule specifies that the application to be created is scheduled to a host that runs applications with the `app: nginx` label.
    -   **Preferred**: Specify rules that are not required to be matched for pod scheduling. In the YAML file, preferred rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. The scheduler attempts to schedule the pod to a node that matches the preferred rules. You can set node weights in preferred rules. Set the other parameters as described in the preceding settings.

**Note:** **Weight**: Set the weight of a preferred rule to a value from 1 to 100. The scheduler calculates the weight of each node that meets the preferred rule, and schedules the pod to the node with the highest weight. |
    |Pod Anti Affinity|Pod anti-affinity rules specify that pods are not scheduled to topology domains where pods with matching labels are deployed. Pod anti-affinity rules apply to the following scenarios:

    -   Schedule the pods of an application to different topology domains, such as multiple hosts. This allows you to enhance the stability of the service.
    -   Grant a pod exclusive access to a node. This enables resource isolation and ensures that no other pod can share the specified node.
    -   Schedule pods of an application to different hosts if the pods interfere with each other.
**Note:** The parameters of pod anti-affinity rules are the same as those of pod affinity rules. You can create the rules for different scenarios. |
    |Toleration|Set toleration rules to allow pods to be scheduled to nodes with matching taints.|
    |Schedule to Virtual Nodes|Specify whether to schedule pods to virtual nodes. This option is unavailable if the cluster does not contain a virtual node.|

4.  In the **Labels and Annotations** section, click **Add** to set one or more labels and annotations for the pod.

    -   Pod Labels: Add labels to the pod.
    -   Pod Annotations: Add annotations to the pod.
5.  Click **Create**.


## Step 4: View applications

After the application is created, you are redirected to the **Complete** page.

1.  You can find the resource objects for the application and click **View Details** to view application details.****

    ![View details](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10983.png)

    After you submit the request, you are redirected to the nginx-deployment details page.

    **Note:** You can also perform the following steps to create Ingresses and services: On the preceding page, click the **Access Method** tab.

    -   Click **Create** next to Service to create a service.
    -   Click **Create** next to Ingress to create an Ingress.
2.  In the left-side navigation pane, choose **Ingresses and Load Balancing** \> **Ingresses**. On the Ingress page, you can find the newly created Ingress.

    ![Ingress rules](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10985.png)

3.  Enter the test domain in the address bar of your browser and press Enter. The NGINX welcome page appears.

    ![NGINX welcome page](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10986.png)


## Related operations

In the left-side navigation pane of the ACK console, click **Clusters**. On the Clusters page, click the name of the cluster or click **Details** in the **Actions** column. In the left-side navigation pane, click **Workload**. The Deployments tab appears. On the Deployments tab, click the name of the application or click **Details** in the **Actions** column. On the details page, you can edit, scale, redeploy, and refresh the application. You can also view the YAML file of the application.

-   Edit: On the details page of the application, click **Edit** in the Actions column of the application to modify the configurations of the application.
-   Scale: On the details page of the application, click **Scale** in the Actions column of the application to scale the application to a required number of pods.
-   View the YAML file: On the details page of the application, click **View in YAML** in the Actions column of the application to update and download the YAML file of the application. You can also save the YAML file as a template.
-   Redeploy: On the details page of the application, click **Redeploy** in the Actions column of the application to redeploy the application.
-   Refresh: On the details page of the application, click **Refresh** to refresh the application information.

## References

-   [t21663.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Create a StatefulSet application by using an image.md)
-   [Create a Linux application by using an orchestration template](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Create a Linux application by using an orchestration template.md)
-   [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md)


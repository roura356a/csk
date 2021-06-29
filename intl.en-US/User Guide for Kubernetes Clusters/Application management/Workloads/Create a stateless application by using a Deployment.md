---
keyword: [orchestration template, image pull Secret, command, create a stateless application]
---

# Create a stateless application by using a Deployment

You can deploy a stateless application by using a Deployment. A Deployment can be created by using an image, an orchestration template, or kubectl commands. Container Service for Kubernetes \(ACK\) allows you to use Secrets to pull images by using a web interface. This topic describes how to create a stateless NGINX application by using an image, an orchestration template, and kubectl commands.

-   An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Your machine is connected to the ACK cluster by using kubectl. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).
-   A private image repository is created and your image is uploaded to the repository. In this topic, an image repository from Container Registry is used. For more information, see [Manage images](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Manage images.md).

## Create a Deployment from an image

**Step 1: Configure basic settings**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  In the upper-right corner of the **Deployments** page, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure the basic settings of the application.

    Before you configure the Deployment, select a namespace in the upper part of the page. In this example, the **default** namespace is selected.

    |Parameter|Description|
    |---------|-----------|
    |Name

|The name of the application. |
    |Replicas

|The number of pods that are provisioned for the application. Default value: 2. |
    |Type

|The type of application. You can select **Deployments**, **StatefulSets**, **Jobs**, ******CronJobs**, or **DaemonSets**. |
    |Label

|Add a label to the application. The label is used to identify the application. |
    |Annotations

|Add an annotation to the application. |
    |Synchronize Timezone

|Specify whether to synchronize the time zone between nodes and containers. |

    **Note:** In this example, **Deployment** is selected as the workload type.

7.  Click **Next**.

    Proceed to the **Container** wizard page.


**Step 2: Configure containers**

On the **Container** wizard page, configure the following container settings: images, resources, ports, environment variables, health checks, lifecycle configurations, volumes, and Log Service configurations.

**Note:** On the **Container** wizard page, Click **Add Container** to add containers to the pods that run the application.

1.  In the **General** section, configure the basic settings of the container.

    |Parameter|Description|
    |---------|-----------|
    |Image Name

|    -   You can click **Select Image**. In the dialog box that appears, select an image and click **OK**. In this example, an NGINX image is selected. On the **Search** tab, select **Docker Images** from the drop-down list, enter NGINX into the search box, and then click Search.
        -   Images from Container Registry: On the **Alibaba Cloud Container Registry** tab, you can select an image from Container Registry. You must select the region and the Container Registry instance to which the image belongs. For more information about Container Registry, see [What is Container Registry?]().

**Note:** On the **Alibaba Cloud Container Registry** tab, you can search for images by name.

        -   Official Docker images: On the **Docker Official Images** tab, you can select a Docker image.
        -   Favorite images: On the **Favorite Images** tab, you can select a Docker image that you have added to your favorite list.
        -   Search for images: On the **Search** tab, you can select Alibaba Cloud Images from the drop-down list and specify a region to search for an image in Container Registry. You can also select Docker Images from the drop-down list and search for a Docker image.
    -   You can also enter the address of a private registry. The registry address must be in the following format: `domainname/namespace/imagename:tag`. |
    |Image Version

|    -   Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.
    -   You can select the following image pull policies:

        -   **ifNotPresent**: If the image that you want to pull is found on your on-premises machine, the image on your on-premises machine is used. Otherwise, ACK pulls the image from the corresponding repository.
        -   **Always**: ACK pulls the image from Container Registry each time the application is deployed or scaled out.
        -   **Never**: ACK uses only images on your on-premises machine.
**Note:** If you select **Image Pull Policy**, no image pull policy is applied.

    -   To pull the image without a password, click **Set Image Pull Secret** to set a Secret that is used to pull the image. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Image/Use aliyun-acr-credential-helper to pull images without a password.md). |
    |Resource Limit

|You can specify upper limits for the CPU, memory, and ephemeral storage resources that the container can consume. This prevents the container from occupying an excessive amount of resources. |
    |Required Resources

|The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable if other services or processes compete for computing resources. |
    |Container Start Parameter

|    -   stdin: Pass stdin to the container.
    -   tty: Stdin is a TeleTYpewriter \(TTY\). |
    |Privileged Container

|    -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
    -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
    |Init Container

|If you select Init Container, an init container is created. An init container provides tools for pod management. For more information, see [init containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx). |

2.  In the **Ports** section, click **Add** to configure ports for the container.

    |Parameter|Description|
    |---------|-----------|
    |Name

|Enter a name for the port. |
    |Container Port

|Specify the container port that you want to expose. The port number must be in the range of 1 to 65535. |
    |Protocol

|Valid values: TCP and UDP. |

3.  In the **Environments** section, click **Add** to set environment variables.

    You can configure environment variables for pods in key-value pairs. Environment variables are used to apply pod configurations to containers. For more information, see [Pod variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

    |Parameter|Description|
    |---------|-----------|
    |Type

|Select the type of environment variable. Valid values:

    -   **Custom**
    -   **ConfigMaps**
    -   **Secrets**
    -   **Value/ValueFrom**
    -   **ResourceFieldRef**
If you select ConfigMaps or Secrets, you can pass all data in the selected ConfigMap or Secret to the container environment variables. In this example, Secrets is selected. Select **Secrets** from the Type drop-down list and select a Secret from the Value/ValueFrom drop-down list. By default, all data in the selected Secret is passed to the environment variable.

![Environment variables](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9007846161/p130410.png)

In this case, the YAML file that is used to deploy the application contains the settings that reference all data in the selected Secret.

![yaml](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9007846161/p130413.jpg) |
    |Variable Key

|Specify the name of the environment variable. |
    |Value/ValueFrom

|Specify a reference that is used to define the environment variable. |

4.  In the **Health Check** section, you can enable **liveness**, **readiness**, and **startup** probes based on your business requirements.

    For more information, see [Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/zh/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

    |Parameter|Request type|Description|
    |---------|------------|-----------|
    |    -   Liveness: Liveness probes are used to determine when to restart the container.
    -   Readiness: Readiness probes are used to determine whether the container is ready to accept traffic.
    -   Startup: Startup probes are used to determine when to start the container.

**Note:** Startup probes are supported in Kubernetes 1.18 and later.

|HTTP

|Sends an HTTP GET request to the container. You can set the following parameters:

    -   Protocol: Select HTTP or HTTPS.
    -   Path: the requested path on the server.
    -   Port: the name or the number of the container port that you want to open. The port number must be in the range of 1 to 65535.
    -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. Key-value pairs are supported.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time \(in seconds\) that the system must wait before it can send a probe to the container after the container is started. Default value: 3.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |
    |TCP

|Sends a TCP socket to the container. kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. You can set the following parameters:

    -   Port: the name or the number of the container port that you want to open. The port number must be in the range of 1 to 65535.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time \(in seconds\) that the system must wait before it can send a probe to the container after the container is started. Default value: 15.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |
    |Command

|Runs a probe command in the container to check the health status of the container. You can set the following parameters:

    -   Command: the probe command that is run to check the health status of the container.
    -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time \(in seconds\) that the system must wait before it can send a probe to the container after the container is started. Default value: 5.
    -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are performed. Default value: 10. Minimum value: 1.
    -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which a probe times out. Default value: 1. Minimum value: 1.
    -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
    -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |

5.  In the **Lifecycle** section, configure the lifecycle of the container.

    You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Configure the lifecycle of a container](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

    ![Lifecycle](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6705677161/p134220.png)

    |Parameter|Description|
    |---------|-----------|
    |Start

|Specify a command and parameter that take effect before the container starts. |
    |Post Start

|Specify a command that takes effect after the container starts. |
    |Pre Stop

|Specify a command that takes effect before the container stops. |

6.  In the **Volume** section, you can mount local volumes and persistent volume claims \(PVCs\) to the container.

    |Parameter|Description|
    |---------|-----------|
    |Add Local Storage

|You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, set Mount Source and Container Path to mount the volume to the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE). |
    |Add PVC

|You can mount persistent volumes \(PVs\) by using persistent volume claims \(PVCs\). You must create a PVC before you can select the PVC to mount a PV. For more information, see [Create a PVC](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Create a PVC.md).|

    In this example, a PVC named disk-ssd is mounted to the /tmp path of the container.

    ![Configure a volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8045359951/p12307.png)

7.  In the **Log** section, you can specify logging configurations and add custom tags to the collected log.

    **Note:** Make sure that the Log Service agent is installed in the cluster.

    |Parameter|Description|
    |---------|-----------|
    |Collection Configuration

|Logstore: Create a Logstore in Log Service to store log data. |
    |Log Path in Container: Specify stdout or a container path to collect log data.

    -   Collect stdout files: If you specify stdout, the stdout files are collected.
    -   Collect log from a path: If you specify a path of the container, log is collected from the specified path. In this example, /var/log/nginx is specified as the path. Wildcard characters can be used in the path. |
    |Custom Tag

|You can also add custom tags. The tags are added to the log of the container when the log is collected. Custom tags make it easy to perform statistical analytics and filtering on log data. |

8.  Click **Next**.

    Proceed to the **Advanced** wizard page.


**Step 3: Configure advanced settings**

On the **Advanced** wizard page, configure the following settings: access control, scaling, scheduling, annotations, and labels.

1.  In the **Access Control** section, you can configure access control settings for exposing backend pods.

    **Note:**

    You can configure the following access control settings based on your business requirements:

    -   Internal applications: For applications that provide services within the cluster, you can create a **ClusterIP** or **NodePort** Service to enable internal communication.
    -   External applications: For applications that are exposed to the Internet, you can configure access control by using one of the following methods:
        -   Create a LoadBalancer Service. When you create a Service, set Type to **Server Load Balancer**. You can select or create a Server Load Balancer \(SLB\) instance for the Service and use the Service to expose your application to the Internet.
        -   Create an Ingress and use it to expose your application to the Internet. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
    You can specify how backend pods are exposed. In this example, a ClusterIP Service and an Ingress are created to expose the NGINX application to the Internet.

    -   To create a Service, click **Create** on the right side of **Services**. In the Create Service dialog box, set the parameters.

        |Parameter|Description|
        |---------|-----------|
        |Name

|The name of the Service.

In this example, nginx-svc is used.|
        |Type

|The type of Service. This parameter determines how the Service is accessed.

        -   **Cluster IP**: The ClusterIP type Service. This type of Service exposes the Service by using an internal IP address of the cluster. If you select this type, the Service is accessible only within the cluster. This is the default type.

**Note:** The **Headless Service** check box is available only when you set Type to **Cluster IP**.

        -   **Node Port**: The NodePort type Service. This type of Service is accessed by using the IP address and a static port of each node. A NodePort Service can be used to route requests to a ClusterIP Service. The ClusterIP Service is automatically created by the system. You can access a NodePort Service from outside the cluster by sending requests to `<NodeIP>:<NodePort>`.
        -   **Server Load Balancer**: The LoadBalancer type Service. This type of Service exposes the Service by using an SLB instance. If you select this type, you can enable internal or external access to the Service. SLB instances can be used to route requests to NodePort and ClusterIP Services.

            -   Create SLB Instance: You can click **Modify** to change the specification of the SLB instance.
            -   Use Existing SLB Instance: You can select an existing SLB instance.
**Note:** You can create an SLB instance or use an existing SLB instance. You can also associate an SLB instance with more than one Service. However, you must take note of the following limits:

            -   If you use an existing SLB instance, the listeners of the SLB instance overwrite the listeners of the Service.
            -   If an SLB instance is created for a Service, you cannot reuse the SLB instance to expose other Services. Otherwise, the SLB instance may be accidentally deleted. Only SLB instances that are manually created in the console or by calling the API can be reused.
            -   An SLB instance must listen on different Service ports if the SLB instance exposes more than one Service. Otherwise, port conflicts may occur.
            -   When you reuse an SLB instance, the names of listeners and vServer groups are used as unique identifiers in Kubernetes. Do not modify the names of listeners and vServer groups.
            -   You cannot use an SLB instance to expose Services across clusters.
**Cluster IP** is selected in this example.|
        |Port Mapping

|Specify a Service port and a container port. The container port must be the same as the one that is exposed in the backend pod. |
        |External Traffic Policy

|        -   Local: Traffic is routed to only the node where the Service is deployed.
        -   Cluster: Traffic can be routed to pods on other nodes.
**Note:** The **External Traffic Policy** parameter is available only when you set Type to **Node Port** or **Server Load Balancer**. |
        |Annotations

|Add an annotation to the Service to modify the configurations of the SLB instance. For example, `service.beta.kubernetes.io/alicloud-loadbalancer-bandwidth:20` specifies that the maximum bandwidth of the Service is 20 Mbit/s. This limits the amount of traffic that flows through the Service. For more information, see [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md). |
        |Label

|Add a label to the Service. |

    -   To create an Ingress, click **Create** on the right side of **Ingresses**. In the Create dialog box, set the parameters.

        For more information about how to configure an Ingress, see [Basic operations of an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).

        **Note:** When you deploy an application from an image, you can create an Ingress for only one Service. In this example, a virtual hostname is specified as the test domain name. You must add a mapping to the hosts file for this domain name in the following format: External endpoint of the Ingress + domain name of the Ingress. In actual scenarios, use a domain name that has obtained an Internet Content Provider \(ICP\) number.

        ```
        101.37.XX.XX   foo.bar.com    # The IP address of the Ingress. 
        ```

        |Parameter|Description|
        |---------|-----------|
        |Name

|The name of the Ingress.

In this example, nginx-ingress is used.|
        |Rules

|Ingress rules are used to enable access to specified Services in a cluster. For more information, see [Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md).

        -   **Domain**: Enter the domain name of the Ingress.
        -   **Path**: Enter the Service URL. The default path is the root path /. The default path is used in this example. Each path is associated with a backend Service. SLB forwards traffic to a backend Service only when inbound requests match the domain name and path.
        -   **Services**: Select a Service and a Service port.
        -   **EnableTLS**: Select this check box to enable TLS. For more information, see [Advanced Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md).
The test domain name `foo.bar.com` is used in this example. The nginx-svc Service is set as the backend of the Ingress. |
        |Weight

|Set the weight for each Service in the path. Each weight is calculated as a percentage value. Default value: 100. |
        |Canary Release

|After a canary release rule is configured, only requests that match the rule are routed to the Service of the new application version. If the weight percentage of a Service is less than 100%, requests that match the rule are routed to the Service based on the weight. ACK supports multiple traffic splitting methods that are applicable to various scenarios, such as canary releases and A/B testing. These methods include:

        -   Traffic splitting based on request headers
        -   Traffic splitting based on cookies
        -   Traffic splitting based on query parameters
**Note:** Only Ingress controllers of version 0.12.0-5 and later support traffic splitting.

The following parameters are required:

        -   **Services**: the Service to be accessed.
        -   **Type**: the type of matching rule, such as Header, Cookie, and Query.
        -   **Name** and **Match Value**: user-defined request parameters that are specified in key-value pairs.
        -   **Matching Rule**: Regular expressions and exact matches are supported. |
        |Annotations

|        -   Click **Rewrite Annotation** to add a rewrite annotation for the Ingress. For example, `nginx.ingress.kubernetes.io/rewrite-target:/` specifies that /path is redirected to the root path /. The root path can be recognized by the backend Service.
        -   You can also click **Add**, and enter the name and value of an annotation. For more information, see [Annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/). |
        |Labels

|Add labels to describe the characteristics of the Ingress. |

    You can find the created Service and Ingress in the **Access Control** section. You can click **Update** or **Delete** to change the configurations.

2.  In the **Scaling** section, specify whether to enable **HPA** and **CronHPA**. This allows you to meet the resource requirements of the application at different load levels.

    -   Horizontal Auto Scaler \(HPA\) can automatically scale the number of pods in an ACK cluster based on the CPU and memory usage.

        **Note:** To enable HPA, you must configure resources that can be scaled for containers. Otherwise, HPA does not take effect.

        |Parameter|Description|
        |---------|-----------|
        |Metric

|Select CPU Usage or Memory Usage. The selected resource type must be the same as that specified in the Required Resources field. |
        |Condition

|Specify the resource usage threshold. The container is scaled out when the threshold is exceeded. |
        |Max. Replicas

|Specify the maximum number of pod replicas to which the application can be scaled. |
        |Min. Replicas

|Specify the minimum number of pod replicas that must run. |

    -   Cron Horizontal Auto Scaler \(CronHPA\) can scale an ACK cluster at a scheduled time. Before you enable CronHPA, you must install ack-kubernetes-cronhpa-controller. For more information about CronHPA, see [Create CronHPA jobs](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md).
3.  In the **Scheduling** section, you can configure the following parameters: **Update Method**, **Node Affinity**, **Pod Affinity**, and **Pod Anti Affinity**. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/?spm=a2c4g.11186623.2.31.3fdd30dfnyevPx#affinity-and-anti-affinity).

    **Note:** During pod scheduling, the node labels and pod labels determine the affinities of the node and pod. You can configure node affinities and pod affinities by using preset labels or by using labels that are manually added.

    |Parameter|Description|
    |---------|-----------|
    |Update Method

|After you select Enable, you can select Rolling Update or OnDelete. For more information, see [Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/?spm=a2c4g.11186623.2.32.3fdd30dfnyevPx). |
    |Node Affinity

|Add labels to worker nodes to set **Node Affinity**.

Node affinity supports required and preferred rules, and various operators, such as In, NotIn, Exists, DoesNotExist, Gt, and Lt.

    -   **Required**: Specify the rules that must be matched for pod scheduling. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. These rules have the same effect as the `NodeSelector` parameter. In this example, pods can be scheduled to only nodes with the specified labels. You can create multiple required rules. However, only one of them must be met.
    -   **Preferred**: Specify the rules that are not required to be matched for pod scheduling. Pods are scheduled to a node that matches the preferred rules when multiple nodes match the required rules. In the YAML file, these rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. In this example, the scheduler attempts to schedule a pod to a node that matches the preferred rules. You can set node weights in preferred rules. If multiple nodes match the required and preferred rules, the node with the highest weight is preferred for pod scheduling. You can create multiple preferred rules. However, all of them must be met before the pod can be scheduled. |
    |Pod Affinity

|Pod affinity specifies that pods can be scheduled to nodes or topological domains where pods with matching labels are deployed. For example, you can use pod affinity to deploy services that communicate with each other to the same topological domain, such as a host. This reduces the network latency between these services.

Pod affinity enables you to select nodes to which pods can be scheduled based on the labels of other running pods. Pod affinity supports required and preferred rules, and the following operators: `In, NotIn, Exists, and DoesNotExist`.

    -   **Required**: Specify rules that must be matched for pod scheduling. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. A node must match the required rules before pods can be scheduled to the node.
        -   **Namespace**: Specify the namespace to apply the required rule. Pod affinity rules are defined based on the labels that are added to pods and therefore must be scoped to a namespace.
        -   **Topological Domain**: Set the topologyKey. This specifies the key for the node label that the system uses to denote the topological domain. For example, if you set the parameter to `kubernetes.io/hostname`, topologies are determined by nodes. If you set the parameter to `beta.kubernetes.io/os`, topologies are determined by the operating systems of nodes.
        -   **Selector**: Click Add to add required labels.
        -   **View Applications**: Click **View Applications** and set the namespace and application in the dialog box that appears. You can view the pod labels on the selected application and select the labels as selectors.
        -   Required Rules: Specify labels of existing applications, the operator, and the label value. In this example, the required rule specifies that the application to be created is scheduled to a host that runs applications with the `app:nginx` label.
    -   **Preferred**: Specify rules that are not required to be matched for pod scheduling. In the YAML file, preferred rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. The scheduler attempts to schedule the pod to a node that matches the preferred rules. You can set node weights in preferred rules. Configure the other parameters as described in the preceding settings.

**Note:** **Weight**: Set the weight in a preferred rule to a value from 1 to 100. The scheduler calculates the weight of each node that meets the preferred rule based on an algorithm, and then schedules the pod to the node with the highest weight. |
    |Pod Anti Affinity

|Pod anti-affinity specifies that pods are not scheduled to topological domains where pods with matching labels are deployed. Pod anti-affinity rules apply to the following scenarios:

    -   Schedule the pods of an application to different topological domains, such as multiple hosts. This allows you to enhance the stability of the service.
    -   Grant a pod exclusive access to a node. This enables resource isolation and ensures that no other pod can share the resources of the specified node.
    -   Schedule pods of an application to different hosts if the pods may interfere with each other.
**Note:** The parameters of pod anti-affinity rules are the same as those of pod affinity rules. You can create the rules for different scenarios. |
    |Toleration

|Configure tolerations to allow pods to be scheduled to nodes with matching taints. |
    |Schedule to Virtual Nodes

|Specify whether to schedule pods to virtual nodes. This parameter is unavailable if the cluster does not contain a virtual node. |

4.  In the **Labels and Annotations** section, click **Add** to configure labels and annotations for the pod.

    |Parameter|Description|
    |---------|-----------|
    |Pod Labels

|Add a label to the pod. The label is used to identify the application. |
    |Pod Annotations

|Add an annotation to the pod configurations. |

5.  Click **Create**.


**Step 4: Check the application**

On the **Complete** wizard page, you can view the created application.

1.  Click **View Details** below **Creation Task Submitted**.

    ![View details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10983.png)

    The nginx-deployment details page appears.

    **Note:** You can also perform the following steps to create an Ingress and a Service: Go to the **Access Method** tab of the application details page, as shown in the preceding figure.

    -   Click **Create** on the right side of Services to create a Service.
    -   Click **Create** on the right side of Ingresses to create an Ingress.
2.  In the left-side navigation pane, choose **Services and Ingresses** \> **Ingresses**. On the Ingresses page, you can find the created Ingress.

    ![Ingress rule](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10985.png)

3.  Enter the test domain name in the address bar of your browser and press Enter. The NGINX welcome page appears.

    ![NGINX welcome page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10986.png)


**What to do next**

**View application details**

In the left-side navigation pane of the ACK console, click **Clusters**. Click the name of the cluster where the application is deployed or click **Details** in the **Actions** column. Choose **Workloads** \> **Deployments**. On the Deployments page, click the name of the deployed application or click **Details** in the **Actions** column.

**Note:** On the Deployments page, you can click **Label**, enter the key and `value` of a label added to the application, and then click **OK** to filter the Deployments.

On the details page of the application, you can view the YAML file of the application. You can also edit, scale, redeploy, and refresh the application.

|Action|Description|
|------|-----------|
|**Edit**|On the details page of the application, click **Edit** in the upper-right corner of the page to modify the application configurations.|
|**Scale**|On the details page of the application, click **Scale** in the upper-right corner of the page to scale the application to a required number of pods.|
|**View in YAML**|On the details page of the application, click **View in YAML** to **update** or **download** the YAML file. You can also click **Save As** to save the file as a different name.|
|**Redeploy**|On the details page of the application, click **Redeploy** in the upper-right corner of the page to redeploy the application.|
|**Refresh**|On the details page of the application, click **Refresh** in the upper-right corner of the page to refresh the application details page.|

**Manage the application**

On the Deployments page, select the application and click **More** in the **Actions** column to perform the following operations.

|Action|Description|
|------|-----------|
|**View in YAML**|View the YAML file of the application.|
|**Redeploy**|Redeploy the application.|
|**Edit Label**|Configure the labels of the application.|
|**Node Affinity**|Configure the node affinity rules of the application. For more information, see [Scheduling](#step_csu_4sv_gz8).|
|**Scaling**|Configure the scaling settings of the application. For more information, see [HPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/HPA.md) and [CronHPA](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md).|
|**Toleration**|Configure the toleration rules of the application. For more information, see [Scheduling](#step_csu_4sv_gz8).|
|**Upgrade Policy**|Configure the upgrade policy of the application.-   **Rolling Update**: Pods are updated in a rolling update fashion.
-   **OnDelete**: All existing pods are deleted before new pods are created. |
|**Clone**|Create a new application by using the same container settings as the current application.|
|**Roll Back**|Roll back the application to a previous version.|
|**Logs**|View the log data of the application.|
|**Delete**|Delete the application.|

## Create a Linux application by using an orchestration template

In an orchestration template, you must define the resources that are required for running an application and use mechanisms such as label selectors to manage these resources.

This section describes how to use an orchestration template to create an NGINX application that consists of a Deployment and a Service. The Deployment provisions pods for the application and the Service manages access to the pods at the backend.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the Deployments page, click **Create from YAML** in the upper-right corner of the page.

6.  Configure the parameters and click **Create**.

    -   **Namespace**: Select the namespace to which the resource objects belong. By default, the default namespace is selected. Most resources are scoped to namespaces, except for underlying computing resources, such as nodes and persistent volumes \(PVs\).
    -   **Sample Template**: ACK provides YAML templates of various resource types. This simplifies the deployment of resource objects. You can also create a custom template based on YAML syntax to define the resources that you want to create.
    -   **Add Deployment**: This feature allows you to define a YAML template.
    -   **Use Existing Template**: You can import an existing template to the configuration page.
    -   **Save As**: You can save the template that you have configured.
    The following sample template is based on an orchestration template provided by ACK. You can use this template to create a Deployment to run an NGINX application.

    **Note:**

    -   ACK supports YAML syntax. You can use the `---` symbol to separate multiple resource objects. This allows you to create multiple resource objects in a single template.
    -   By default, if you mount a volume to the application, the existing files in the mount path of the application are overwritten. To avoid the existing files from being overwritten, you can add a `subPath` parameter.
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
              volumeMounts:
                - name: nginx-config
                  mountPath: /etc/nginx/nginx.conf 
                  subPath: nginx.conf   # Set the subPath parameter.     
          volumes:
            - name: nginx-config
              configMap:
                name: nginx-conf
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
       type: type: LoadBalancer        ## In this example, the Service type is changed from Nodeport to LoadBalancer. 
    ---
    
    # The ConfigMap of the mounted volume.
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: nginx-conf
      namespace: default
    data:
      nginx.conf: |-
       user  nginx;
       worker_processes  1;
       error_log  /var/log/nginx/error.log warn;
       pid        /var/run/nginx.pid;
       events {
            worker_connections  1024;
        }
        http {
            include       /etc/nginx/mime.types;
            default_type  application/octet-stream;
            log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                              '$status $body_bytes_sent "$http_referer" '
                              '"$http_user_agent" "$http_x_forwarded_for"';
            access_log  /var/log/nginx/access.log  main;
            sendfile        on;
            #tcp_nopush     on;
            keepalive_timeout  65;
            #gzip  on;
            include /etc/nginx/conf.d/*.conf;
        } 
    ```

7.  Click **Create**. A message that indicates the deployment status appears.


## Manage applications by using commands

This topic describes how to create an application or view containers of an application by using commands.

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

In the CLI, run the `kubectl get pods` command to list all running containers in the default namespace.

```
NAME                                   READY     STATUS    RESTARTS   AGE
nginx-2721357637-d****                 1/1       Running   1          9h
```

## Create an application by using an image pull Secret

Container Service for Kubernetes \(ACK\) allows you to use image pull Secrets in the ACK console. You can create an image pull Secret or use an existing image pull Secret.

When you create an application from a private image, you must set a Secret for image pulling to ensure the security of the image. In the ACK console, you can specify the authentication information of the private image repository as a Secret of the docker-registry type. This Secret applies to the specified Kubernetes cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** page, select the namespace and click **Create from Image** in the upper-right corner of the page.

6.  On the **Basic Information** wizard page, set the parameters. For more information, see [Create a stateless application by using a Deployment](#task_p2s_2rl_vdb).

7.  Configure containers.

    The following example describes how to configure an image pull Secret. For more information about container configurations, see [Create a stateless application by using a Deployment](#task_p2s_2rl_vdb).

    1.  On the **Container** wizard page, enter the address of the private image in the **Image Name** field. The address must be in the following format: `domainname/namespace/imagename`.

        **Note:** Public images do not require Secrets.

    2.  Enter the image version that you want to use in the Image Version field.

        A Tomcat private image is used in this example.

        ![Ingress settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13583.png)

    3.  Click **Set Image Pull Secret**, and create a Secret or select an existing Secret in the dialog box that appears.

        -   You can select **New Secret**. If you select New Secret, set the following parameters:

            |Parameter|Description|
            |---------|-----------|
            |**Name**|The key name of the Secret. You can enter a custom name.|
            |**Repository Domain**|The address of the specified Docker registry. If you use an image repository in Container Registry, the address of the image repository is automatically displayed.|
            |**Username**|The username used to log on to the Docker repository. If you use an image repository in Container Registry, your account name is used as the username. Alibaba Cloud accounts and Resource Access Management \(RAM\) users are supported.|
            |**Password**|The password used to log on to the Docker repository. If you use an image repository in Container Registry, the password is the logon password of Container Registry.|
            |**Email**|The email address. This parameter is optional.|

            Click **OK**. The newly created Secret appears on the page.

            ![Specify a Secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13585.png)

        -   You can also select **Existing Secret**. You can use commands or YAML files to create image pull Secrets. For more information, see [How do I use private images in Kubernetes clusters?]() and [Create an application from a private image repository](/intl.en-US/Quick Start/Advanced operations/Create an application from a private image repository.md).

            ![Existing Secret](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4245359951/p13586.png)

8.  Configure other parameters based on your requirements. Then, click **Create**.

    For more information, see Step 3: Configure advanced settings.

9.  In the left-side navigation pane of the ACK console, click **Clusters**.

10. On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

11. In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

12. On the Deployments page, check the status of the application.

    Verify that the Tomcat application runs as expected. This indicates that the Tomcat private image is pulled by using the image pull Secret.


## References

-   [Use a StatefulSet to create a stateful application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a StatefulSet to create a stateful application.md)
-   [Use annotations to configure load balancing](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Use annotations to configure load balancing.md)


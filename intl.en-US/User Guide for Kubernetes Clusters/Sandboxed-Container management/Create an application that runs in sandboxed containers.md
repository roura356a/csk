# Create an application that runs in sandboxed containers

This topic describes how to use an image to create an NGINX application that runs in sandboxed containers. The NGINX application is accessible over the Internet.

A cluster of Container Service for Kubernetes \(ACK\) that runs sandboxed containers is created. For more information, see [Create a managed Kubernetes cluster that runs sandboxed containers](/intl.en-US/User Guide for Kubernetes Clusters/Sandboxed-Container management/Create a security sandbox cluster/Create a managed Kubernetes cluster that runs sandboxed containers.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com)[ACK console](https://partners-intl.console.aliyun.com/#/cs).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** tab, click **Create from Image**.

6.  On the **Basic Information** wizard page, set the basic information and parameters for the application, and then click **Next**.

    Set **Name**, **Replicas**, **Type**, **Annotations**, and **Label**. Specify whether to **synchronize timezone from node to container**. The number of replicas equals the number of replicated pods provisioned for the application.

    **Note:**

    In this example, **Deployment** is selected as the application type.

    -   In this example, **Deployment** is selected as the application type.
    -   **Container Runtime**: You can select `runc` or `runv`. Default value: `runc`. To create an application that runs in sandboxed containers, select `runv`.
    If you do not specify a **namespace**, the default namespace is selected.

7.  Configure the containers.

    **Note:** At the top of the **Container** wizard page, click **Add Container** to add more containers for the application.

    The following parameters are required to configure the containers.

    -   General

        ![General settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7045359951/p12305.png)

        |Parameter|Description|
        |---------|-----------|
        |Image Name|Click **Select Image**. In the dialog box that appears, select an image and click **OK**. In this example, the NGINX image is selected.You can also enter the address of a private registry. The registry address must be in the following format: `domainname/namespace/imagename:tag`. |
        |Image Version

|        -   Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.
        -   You can select the following image pull policies:

            -   **ifNotPresent**: If the image you want to pull is found in the region where the cluster is deployed, the local image is used. Otherwise, ACK pulls the image from the corresponding repository.
            -   **Always**: ACK pulls the image from the repository each time the application is deployed or expanded.
            -   **Never**: ACK uses only local images.
**Note:** If you select **Image Pull Policy**, no image pull policy is applied for the deployment of the application.

        -   To pull the image without a secret, click **Set Image Pull Secret** to set a secret for pulling images. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use aliyun-acr-credential-helper to pull images without a password.md). |
        |Resource Limit|You can specify an upper limit for the CPU, memory, and ephemeral storage resources that the container can consume. This prevents the container from occupying an excessive amount of resources. The CPU resource is measured in milicores \(one thousandth of one core\). The memory resource is measured in MiB. The ephemeral storage resource is measured in GiB.|
        |Required Resources|The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This prevents the application from becoming unavailable when other Services or processes occupy these resources.|
        |Container Start Parameter|        -   stdin: specifies that start parameters defined in the console are sent to the Linux system.
        -   tty: specifies that start parameters defined in a virtual terminal are sent to the console. |
        |Privileged Container|        -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
        -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
        |Init Container|If you select Init Container, an init container is created. An init container provides tools to manage pods. For more information, see [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx).|

    -   Ports

        Specify the container port.

        -   Name: Enter a name for the container port.
        -   Container Port: the container port that you want to open. Enter a port number from 1 to 65535.
        -   Protocol: Select TCP or UDP.
    -   Environments

        You can set environment variables in key-value pairs for pods. Environment variables are used to apply pod configurations to containers. For more information, see [Pod variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

        -   Type: the type of environment variable. You can select **Custom**, **ConfigMaps**, **Secret**, **Value/ValueFrom**, or **ResourceFieldRef**. If you select ConfigMaps or Secret as the type of environment variable, all values in the selected ConfigMaps or Secret are passed to the container environment variable. In this example, Secret is selected.

            Select **Secret** from the Type drop-down list and select a Secret from the **Value/ValueFrom** drop-down list. All values in the selected Secret are passed to the environment variable.

            In this case, the YAML file that is used to deploy the application contains the settings that reference all values in the specified Secret.

        -   Variable Key: Specify the key of the environment variable.
        -   Value/ValueFrom: Specify the value that is referenced by the environment variable.
    -   Health Check

        Health check settings include liveness and readiness probes. Liveness probes determine when to restart the container. Readiness probes indicate whether the container is ready to accept network traffic. For more information about health checks, see [Configure Liveness, Readiness, and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

        |Request type|Description|
        |------------|-----------|
        |HTTP|Sends an HTTP GET request to the container. You can set the following parameters:        -   Protocol: HTTP or HTTPS.
        -   Path: the requested path on the server.
        -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
        -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. You can set HTTP headers in key-value pairs.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send the first probe to a launched container. Default value: 3.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are sent. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the timeout period \(in seconds\) of probes. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |
        |TCP|Sends a TCP socket to the container. Kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, the container is considered unhealthy. Supported parameters include:        -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send the first probe to the launched container. Default value: 15.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are sent. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the timeout period \(in seconds\) of probes. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |
        |Command|Runs a probe command in the container to check the health status of the container. Supported parameters include:        -   Command: the probe command that is run to check the health status of the container.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the time period \(in seconds\) that the system must wait before it can send the first probe to the launched container. Default value: 5.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the interval \(in seconds\) at which probes are sent. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the timeout period \(in seconds\) of probes. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of times that an unhealthy container must consecutively pass health checks before it is considered healthy. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of times that a healthy container must consecutively fail health checks before it is considered unhealthy. Default value: 3. Minimum value: 1. |

    -   Lifecycle

        You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Configure the lifecycle of a container](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Start**: Set the command and parameter that take effect before the container starts.
        -   **Post Start**: Set the command that takes effect after the container starts.
        -   **Pre Stop**: Set the command that takes effect before the container stops.
    -   Volume

        You can mount local storage volumes and persistent volume claims \(PVCs\) to the container.

        -   **Local Storage**: You can select HostPath, ConfigMap, Secret, and EmptyDir. The storage volume is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **PVC**: Select Cloud Storage.
        In this example, a PVC named disk-ssd is mounted to the /tmp path of the container.

        ![Configure the volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8045359951/p12307.png)

    -   Log

        Configure **Log Service**. You can specify collection configurations and custom tags.

        **Note:** Make sure that the Log Service agent is installed in the cluster.

        |Parameter|Description|
        |---------|-----------|
        |Collection Configuration|Logstore: Create a Logstore in Log Service to store collected logs.|
        |Log Path in Container: Specify stdout or a path to collect logs.

        -   stdout: specifies that the stdout files are collected.
        -   Text Logs: specifies that logs in the specified path of the container are collected. In this example, /var/log/nginx is specified as the path. Wildcard characters can be used in this path. |
        |Custom Tag|You can also add tags. Tags are added to the logs of the container when the logs are collected. Log data with tags is easier to aggregate and filter.|

8.  Set the preceding parameters based on your business requirements and click **Next**.

9.  Configure advanced settings.

    -   Access Control

        **Note:**

        You can configure the following access control settings based on your business requirements:

        -   Internal applications: For applications that run inside the cluster, you can create a Service of the ClusterIP or NodePort type to enable internal communication.
        -   External applications: For applications that are open to the Internet, you can configure access control by using one of the following methods:
            -   Create a LoadBalancer Service that uses a Server Load Balancer \(SLB\) instance and use the Service to expose your application to the Internet.
            -   Create an Ingress and use the Ingress to expose your application to the Internet. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
        Specify how backend pods are exposed. In this example, a ClusterIP Service and an Ingress are created to expose the NGINX application to the Internet.

        |Parameter|Description|
        |---------|-----------|
        |Services|Click **Create** on the right side of **Services**. In the Create Service dialog box, set the parameters. For more information about the parameters that are required to create a Service, see [Manage Services](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Manage Services.md). **Cluster IP** is selected in this example.|
        |Ingresses|Click **Create** on the right side of **Ingresses**. In the Create dialog box, set the parameters. For more information about the parameters that are required to create an Ingress, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Basic operations of an Ingress.md).**Note:** When you deploy an application from an image, you can create an Ingress for only one Service. In this example, the name of a virtual host is specified as the test domain name. You must add a mapping rule for this domain name to the hosts file, as shown in the following code block. In practical scenarios, use a domain name that has obtained an Internet Content Provider \(ICP\) number.

        ```
101.37.224.146   foo.bar.com    #The IP address of the Ingress.
        ``` |

        You can find the created Service and Ingress in the **Access Control** section. Click **Update** or **Delete** to modify the settings.

    -   Scaling

        In the **Scaling** section, specify whether to enable **HPA** and **CronHPA** based on your business requirements. Horizontal Pod Autoscaler \(HPA\) enables the application to run at different load levels.

        ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4104519061/p10978.png)

        -   HPA can automatically scale the number of pods in a Container Service for Kubernetes \(ACK\) cluster based on the CPU and memory usage.

            **Note:** To enable HPA, you must configure resources that can be scaled for the container. Otherwise, HPA does not take effect.

            |Parameter|Description|
            |---------|-----------|
            |Metric|Select CPU Usage or Memory Usage. The selected resource type must be the same as that specified in the Required Resources field.|
            |Condition|Specify the resource usage threshold. HPA triggers scaling activities when the threshold is exceeded.|
            |Max. Replicas|Specify the maximum number of replicated pods to which the application can be scaled.|
            |Min. Replicas|Specify the minimum number of replicated pods that must run.|

        -   CronHPA can scale an ACK cluster at a scheduled time. For more information about CronHPA, see [Create CronHPA jobs](/intl.en-US/User Guide for Kubernetes Clusters/Auto Scaling/CronHPA.md).
    -   Scheduling

        You can set the following parameters: Update Method, Node Affinity, Pod Affinity, Pod Anti Affinity, and Toleration. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/?spm=a2c4g.11186623.2.31.3fdd30dfnyevPx#affinity-and-anti-affinity).

        **Note:** During pod scheduling, the labels of a node and a pod determine the affinities of the node and pod. You can configure node affinity and pod affinity by selecting preset labels or by manually adding labels.

        |Parameter|Description|
        |---------|-----------|
        |Update Method|Select Rolling Update or OnDelete. For more information, see [Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/?spm=a2c4g.11186623.2.32.3fdd30dfnyevPx). |
        |Node Affinity|Add labels to worker nodes to set **Node Affinity**.Node affinity supports required and preferred rules, and various operators, such as In, NotIn, Exists, DoesNotExist, Gt, and Lt.

        -   **Required**: Specify the rules that must be matched for a pod to be scheduled to a node. Required rules correspond to the requiredDuringSchedulingIgnoredDuringExecution affinity, which is conceptually similar to `NodeSelector`. In this example, pods can be scheduled to only worker nodes with specified labels. You can create more than one required rule. However, only one required rule must be matched.
        -   **Preferred**: Specify the rules that are preferred to match. Preferred rules correspond to the preferredDuringSchedulingIgnoredDuringExecution affinity. In this example, the scheduler attempts to avoid scheduling the pod to a node that matches the preferred rules. You can set node weights in preferred rules. If multiple nodes match the preferred rules, the pod is preferably scheduled to the node with the highest weight. You can create more than one preferred rule. All preferred rules must be matched before a pod can be scheduled to a preferred node. |
        |Pod Affinity|Pod affinity specifies that a pod is scheduled to a node in the same topological domain if the node runs a pod that matches the affinity rules. For example, you can use pod affinity to deploy Services that communicate with each other to the same topological domain, such as a host. This reduces the network latency between these Services.You can enforce pod affinity by using the labels of the pods that run on a node. Pod affinity supports required and preferred rules, and the following operators: `In, NotIn, Exists, and DoesNotExist`.

        -   **Required**: Specify the rules that must be matched for a pod to be scheduled to a node. Required rules correspond to the requiredDuringSchedulingIgnoredDuringExecution affinity. A node must match the required rules before a pod can be scheduled to the node.
            -   **Namespace**: Specify a namespace rule. Pod affinity is scoped to namespaces because it is enforced based on the labels of pods
            -   **Topological Domain**: Set the topologyKey. This specifies the key for the node label that the system uses to denote the topological domain. For example, if you set the parameter to `kubernetes.io/hostname`, topologies are determined by nodes. If you set the parameter to `beta.kubernetes.io/os`, topologies are determined by the operating systems of nodes.
            -   **Selector**: Click Add to add pod labels.
            -   **View Applications**: Click **View Applications** and set the namespace and applications in the dialog box that appears. You can view the pod labels on the selected application and add the labels as selectors.
            -   Required Rules: Specify labels on existing applications, operators, and label values. In this example, the required rule specifies that the application to be created is scheduled to a host that runs applications with the `app:nginx` label.
        -   **Preferred**: Specify the rules that are preferred to match. Preferred rules correspond to the preferredDuringSchedulingIgnoredDuringExecution affinity. The scheduler attempts to schedule a pod to a node that matches the preferred rules. You can set node weights in preferred rules. Set the other parameters as described in the preceding settings.

**Note:** **Weight**: Set the weight of a preferred rule to a value from 1 to 100. The scheduler calculates the weight of each node that meets the preferred rule, and then schedules the pod to the node with the highest weight. |
        |Pod Anti Affinity|Pod anti-affinity specifies that a pod is not scheduled to a node in the same topological domain if the node runs a pod that matches the anti-affinity rules. Pod anti-affinity rules apply to the following scenarios:

        -   Schedule the pods of a Service to different topological domains, such as multiple hosts. This allows you to enhance the stability of the Service.
        -   Grant a pod exclusive access to a node. This enables resource isolation and ensures that no other pod can share the resources of the specified node.
        -   Schedule pods of Services to different hosts if these Services may interfere each other.
**Note:** You can set pod anti-affinity rules in the same way as setting pod affinity rules, and choose anti-affinity or pod affinity as needed. |
        |Toleration|Set toleration rules to allow pods to be scheduled to nodes with matching taints.|
        |Schedule to Virtual Nodes|Specify whether to schedule pods to virtual nodes. This option is unavailable if the cluster does not contain a virtual node.|

    -   Labels and Annotations
        -   Pod Labels: Add a label to the pod. The label is used to identify the application.
        -   Pod Annotations: Add an annotation to the pod.
10. Click **Create**.

    After the application is deployed, you are redirected to the Complete page. You can find the resource objects under the application and click **View Details** to view application details.

    ![View details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10983.png)

11. Go to the details page of the cluster. In the left-side navigation pane, click **Ingresses**.

    You can find that the newly created Ingress rule appears on the page.

    ![Ingress rules](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10985.png)


Enter the test domain into the address bar of your browser and press the Enter key. The NGINX welcome page appears.

![NGINX welcome page](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0665359951/p10986.png)


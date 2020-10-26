# Use an image to create a stateful application

You can use an image to create a stateful application. This topic describes how to use an image to create a stateful NGINX application in the Container Service for Kubernetes \(ACK\) console. This topic also describes the features of StatefulSets.

Before you deploy the NGINX application, perform the following steps:

-   [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md)
-   [Create a Persistent Volume Claim](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Create a Persistent Volume Claim.md)
-   [Use kubectl to connect to a cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to a cluster.md)

StatefulSets provide the following features:

|Scenario|Description|
|--------|-----------|
|Pod consistency|Pod consistency is used. This allows you to make sure that pods are launched and terminated in the defined order. This also allows you to make sure that the consistency of networks is high. Pod consistency is based on pod configurations, regardless of the node to which a pod is scheduled.|
|Stable and persistent storage|VolumeClaimTemplate allows you to mount persistent volumes \(PVs\) to pods. Mounted PVs are not deleted if pod replicas are deleted or scaled in.|
|Stable network identifiers|Each pod in a StatefulSet derives its hostname from the name of the StatefulSet and the ordinal of the pod. The pattern for the hostname is `StatefulSet name-pod ordinal`.|
|Ordinal pod indexes|For a StatefulSet with N pod replicas, each pod is assigned an integer ordinal from 0 to N-1. The ordinals assigned to pods within the StatefulSet are unique.|

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  Click the **StatefulSets** tab. On the **StatefulSets** tab, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure the basic settings.

    In this example, an application of the **StatefulSet** type is created.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Namespace|The namespace in which the application is deployed. The default namespace is automatically selected. You can select another namespace.|
    |Replicas|The number of replicated pods that are provisioned for the application.|
    |Type|The type of the application. You can select **Deployments**, **StatefulSets**, **Jobs**, **Cron Jobs**, or **DaemonSets**.****|
    |Labels|Add labels to the application.|
    |Annotations|Add annotations to the application.|
    |Synchronize Timezone|Specify whether to synchronize the timezone from nodes to containers.|

7.  Click **Next** to proceed to the **Container** wizard page.

8.  Configure the containers.

    **Note:** At the top of the **Container** wizard page, click **Add Container** to add more containers for the application.

    The following parameters are required to configure the containers.

    -   Basic settings

        ![Basic settings](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7045359951/p12305.png)

        |Parameter|Description|
        |---------|-----------|
        |Image Name|        -   To use a Docker image or an image from Container Registry \(ACR\), click **Select Image**. In the dialog box that appears, select an image and click **OK**. In this example, the NGINX image is selected. In the Select Image dialog box, click the **Search** tab, select **Docker Images** from the drop-down list, enter NGINX in the search bar, and then click Search.
            -   Select an image from Alibaba Cloud Container Registry \(ACR\): In the Select Image dialog box, click the **Alibaba Cloud Container Registry** tab and select an image. You must first select a region and an ACR instance. For more information about ACR, see [What is Container Registry Enterprise Edition?]().

**Note:** On the **Alibaba Cloud Container Registry** tab, you can search for an image from ACR by name.

            -   Select a Docker image: In the Select Image dialog box, click the **Docker Official Images** tab and select a Docker image.
            -   Select a favorite image: In the Select Image dialog box, click the **Favorite Images** tab and select a favorite image.
            -   Search for a Docker image or an image from ACR: In the Select Image dialog box, click the **Search** tab and search for a Docker image or an image in a specified region from ACR by name.
        -   To use a private image, enter the address of a private image registry. The registry address must be in the `domainname/namespace/imagename:tag` format. |
        |Image Version

|        -   Click **Select Image Version** and select an image version. If you do not specify an image version, the latest image version is used.
        -   You can select the following image pull policies:

            -   **ifNotPresent**: If the image you want to pull is found in the region where the cluster is deployed, the local image is used. Otherwise, ACK pulls the image from the corresponding repository.
            -   **Always**: ACK pulls the image from the repository each time the application is deployed or expanded.
            -   **Never**: ACK uses only local images.
**Note:** If you select **Image Pull Policy**, no image pull policy is applied for the deployment of the application.

        -   To pull the image without a secret, click **Set Image Pull Secret** to set a secret for pulling images. For more information, see [Use aliyun-acr-credential-helper to pull images without a password](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use aliyun-acr-credential-helper to pull images without a password.md). |
        |Resource Limit|You can specify an upper limit for the CPU, memory, and ephemeral storage space that the container can consume. This allows you to make sure that the container does not occupy unnecessary resources. The CPU resource is measured in millicores \(one thousandth of one core\). The memory resource is measured in MiB. The ephemeral storage resource is measured in GiB.|
        |Required Resources|The amount of CPU and memory resources that are reserved for this application. These resources are exclusive to the container. This allows you to make sure that the application is still available when other services or processes occupy these resources.|
        |Container Start Parameter|        -   stdin: specifies that start parameters defined in the ACK console are imported to the Linux system.
        -   tty: specifies that start parameters defined in a virtual terminal are imported to the ACK console. |
        |Privileged Container|        -   If you select Privileged Container, privileged=true is set for the container and the privilege mode is enabled.
        -   If you do not select Privileged Container, privileged=false is set for the container and the privilege mode is disabled. |
        |Init Container|If you select Init Container, an init container is created. An init container provides tools to manage pods. For more information, see [Init Containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/?spm=a2c4g.11186623.2.13.3fdd30dfnyevPx).|

    -   Ports

        Specify the container ports.

        -   Name: Enter a name for a port.
        -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
        -   Protocol: Select TCP or UDP.
    -   Environments

        You can use key-value pairs to set environment variables for pods. Environment variables are used to apply pod configurations to containers. For more information, see [Expose Pod Information to Containers Through Environment Variables](https://kubernetes.io/docs/tasks/inject-data-application/environment-variable-expose-pod-information/?spm=0.0.0.0.8VJbrE).

        -   Type: the type of the environment variable. You can select **Custom**, **ConfigMaps**, **Secret**, or **Value/ValueFrom**. If you select ConfigMaps or Secret as the type of the environment variable, all values in the selected ConfigMap or Secret are passed to the container environment variables. In this example, Secret is selected.

            Select **Secret** from the Name drop-down list and select a Secret from the **Value/ValueFrom** drop-down list. All values in the selected Secret are passed to the environment variable.

            In this case, the YAML file that is used to deploy the application contains the required settings. These settings are used to reference all values in the specified Secret.

        -   Variable Key: Specify the key of the environment variable.
        -   Value/ValueFrom: This is the reference that is used to define the environment variable.
    -   Health check

        Health check settings include liveness and readiness probes. Liveness probes determine when to restart the container. Readiness probes indicate whether the container is ready to accept traffic. For more information about health checks, see [Configure Liveness, Readiness, and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

        |Request type|Description|
        |------------|-----------|
        |HTTP request|Sends an HTTP GET request to the container. You can set the following parameters:        -   Protocol: HTTP or HTTPS.
        -   Path: the requested path on the server.
        -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
        -   HTTP Header: the custom headers in the HTTP request. Duplicate headers are allowed. Key-value pairs are supported.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 15.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |
        |TCP connection|Opens a TCP socket to the container. The kubelet attempts to open the socket on the specified port. If the connection can be established, the container is considered healthy. Otherwise, it is considered unhealthy. You can set the following parameters:        -   Port: the container port that you want to open. Enter a port number from 1 to 65535.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 15.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |
        |Command|Runs a probe command in the container to check the health status. You can set the following parameters:        -   Command: the probe command that is run to check the health status of the container.
        -   Initial Delay \(s\): the initialDelaySeconds field in the YAML file. This field specifies the wait time \(in seconds\) before the first probe is performed after the container is started. Default value: 5.
        -   Period \(s\): the periodSeconds field in the YAML file. This field specifies the frequency \(in seconds\) that the probe is performed. Default value: 10. Minimum value: 1.
        -   Timeout \(s\): the timeoutSeconds field in the YAML file. This field specifies the time \(in seconds\) after which the probe times out. Default value: 1. Minimum value: 1.
        -   Healthy Threshold: the minimum number of consecutive successful probes that must occur for a container to be considered healthy after a failed probe. Default value: 1. Minimum value: 1. For liveness probes, this parameter must be set to 1.
        -   Unhealthy Threshold: the minimum number of consecutive failed probes that must occur for a container to be considered unhealthy after a successful probe. Default value: 3. Minimum value: 1. |

    -   Lifecycle

        You can set the following parameters to configure the lifecycle of the container: Start, Post Start, and Pre Stop. For more information, see [Attach Handlers to Container Lifecycle Events](https://kubernetes.io/docs/tasks/configure-pod-container/attach-handler-lifecycle-event/).

        -   **Start**: Set the command and parameter that take effect before the container starts.
        -   **Post Start**: Set the command that takes effect after the container starts.
        -   **Pre Stop**: Set the command that takes effect before the container starts.
    -   Volume

        You can mount local storage volumes and persistent volume claims \(PVCs\) to the container.

        -   **Local Storage**: You can select hostPath, ConfigMap, Secret, and EmptyDir. The source directory or file is mounted to a path in the container. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
        -   **PVC**: Select Cloud Storage.
        In this example, a PVC named disk-ssd is mounted to the /tmp path of the container.

        ![Configure volumes](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8045359951/p12307.png)

    -   Log configuration

        Configure **Log Service**. You can specify collection methods and custom tags.

        **Note:** Make sure that the Log Service agent has been installed for the cluster.

        |Parameter|Description|
        |---------|-----------|
        |Collection configuration|Logstore: Create a Logstore in Log Service to store log data.|
        |Log Path: Specify stdout or a path to collect logs.

        -   stdout: specifies that the stdout files are collected.
        -   Text Logs: specifies that logs in the specified path of the container are collected. In this example, /var/log/nginx is specified as the path. Wildcard characters can be used in the path. |
        |Custom tags|You can also set custom tags. Custom tags are added to the logs of the container when the logs are collected. Custom tags provide an easy method to filter collected logs and perform statistical analytics.|

9.  Set the preceding parameters based on your business requirements and click **Next**.

10. Configure advanced settings

    -   Access Control

        **Note:**

        You can configure the following access control settings based on your business requirements:

        -   Internal applications: For applications that run inside the cluster, you can create a service of the Cluster IP or Node Port type to enable internal communication.
        -   External applications: For applications that are open to the Internet, you can configure the access control by using one of the following methods:
            -   Create a service of the LoadBalancer type and enable access to your application over the Internet by using a Server Load Balancer \(SLB\) instance.
            -   Create an Ingress to route external access to a service inside a cluster. For more information, see [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/?spm=a2c4g.11186623.2.23.3fdd30dfnyevPx).
        Configure the access control settings to enable access to pods that run the application. In this example, a Cluster IP service and an Ingress are created to enable access to the NGINX application over the Internet.

        |Parameter|Description|
        |---------|-----------|
        |Service|Click **Create** on the right side of **Service**. In the **Create Service** dialog box, set the parameters. For more information about the parameters that are required to create a service, see [t16662.md\#](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Create a service.md). Select **Cluster IP**.|
        |Ingress|Click **Create** on the right side of **Ingress**. In the Create dialog box, set the parameters. For more information about the parameters that are required to create an Ingress, see [Ingress configurations](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Ingress management/Ingress configurations.md).**Note:** When you deploy an application from an image, you can create an Ingress for only one service. In this example, a virtual host name is used as the test domain name. You must add the following entry to the hosts file to map the domain name to the IP address of the Ingress. In actual scenarios, use a domain name that has obtained an ICP number.

        ```
101.37.224.146   foo.bar.com    #The IP address of the Ingress.
        ``` |

        You can find the newly created service and Ingress in the **Access Control** section. Click **Update** or **Delete** to modify the settings.

        ![Update and delete the Ingress](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10982.png)

    -   Scaling settings

        Specify whether to enable **HPA** to automatically scale the number of pods based on the CPU and memory usage. This enables the application to run as expected at different load levels.

        ![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0665359951/p10978.png)

        **Note:** To enable Horizontal Pod Autoscaler \(HPA\), you must configure resources that support scaling for the container. Otherwise, HPA does not take effect.

        -   **Metric**: Select CPU Usage or Memory Usage. The selected resource type must be the same as the one you have specified in the Required Resources field.
        -   **Condition**: Specify the resource usage threshold. HPA triggers scaling events when the threshold is exceeded.
        -   **Max. Replicas**: Specify the maximum number of replicated pods to which the application can be scaled.
        -   **Min. Replicas**: Specify the minimum number of replicated pods that must run.
    -   Scheduling settings

        You can set the following parameters: Update Method, Node Affinity, Pod Affinity, Pod Anti Affinity, and Toleration. For more information, see [Affinity and anti-affinity](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/?spm=a2c4g.11186623.2.31.3fdd30dfnyevPx#affinity-and-anti-affinity).

        **Note:** Node affinity and pod affinity affect pod scheduling based on node labels and pod labels. You can add node labels and pod labels that are provided by Kubernetes to node affinity and pod affinity. You can also add custom labels to nodes and pods, and then configure node affinity and pod affinity based on these custom labels.

        |Parameter|Description|
        |---------|-----------|
        |Update Method|Select Rolling Update or OnDelete. For more information, see [Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/?spm=a2c4g.11186623.2.32.3fdd30dfnyevPx). |
        |Node Affinity|Add labels to worker nodes to set the node affinity.****Node Affinity supports required and preferred rules, and various operators, such as In, NotIn, Exists, DoesNotExist, Gt, and Lt.

        -   **Required**: Set node labels that must be matched for pod scheduling. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. These rules have the same effect as the `NodeSelector` parameter. In this example, pods can be scheduled to only nodes with the specified labels. You can create multiple required rules. However, only one of the rules must be met.
        -   **Preferred**: Specify the node weight and node labels that are not required to be matched for pod scheduling. Pods are scheduled to a node that matches the preferred rules when multiple nodes match the required rules. In the YAML file, these rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the nodeAffinity parameter. In this example, the scheduler attempts to schedule the pod to a node that matches the preferred rules. You can set node weights in preferred rules. If multiple nodes match the required and preferred rules, the node with the highest weight is preferred for pod scheduling. You can create multiple preferred rules. However, all of the rules must be met before the pods are scheduled. |
        |Pod Affinity|Pod affinity specifies that pods can be scheduled to nodes or topological domains where pods with matching labels are deployed. For example, you can use pod affinity to deploy services that communicate with each other to the same topology domain, such as a host. This reduces the network latency between these services.Pod affinity enables you to specify which node pods can be scheduled based on the labels on other running pods. Pod affinity supports required and preferred rules, and various operators, such as `In, NotIn, Exists, and DoesNotExist`.

        -   **Required**: Specify rules that must be matched for pod scheduling. In the YAML file, these rules are defined by the requiredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. A node must match the required rules before pods can be scheduled to the node.
            -   **Namespace**: Specify the namespace to apply the required rule. Pod affinity rules are defined based on the labels that are added to pods. In this case, labels are limited by namespace constraints.
            -   **Topological Domain**: Set the topologyKey. This specifies the key for the node label that the system uses to denote the topology domain. For example, if you set the parameter to `kubernetes.io/hostname`, nodes are used to define topologies. If you set the parameter to `beta.kubernetes.io/os`, the operating systems of nodes are used to determine topologies.
            -   **Selector**: Click Add to add pod labels.
            -   **View Applications**: Click **View Applications** and set the namespace and application in the dialog box that appears. You can view the pod labels on the selected application and add the labels as selectors.
            -   Required rules: Specify labels on existing applications, the operator, and the label value. In this example, the required rule specifies that the application to be created is scheduled to a host that runs applications with the `app: nginx` label.
        -   **Preferred**: Specify rules that are not required to be matched for pod scheduling. In the YAML file, preferred rules are defined by the preferredDuringSchedulingIgnoredDuringExecution field of the podAffinity parameter. The scheduler attempts to schedule the pod to a node that matches the preferred rules. You can set node weights in preferred rules. Set the other parameters as described in the preceding settings.

**Note:** **Weight**: Set the weight of a preferred rule to a value from 1 to 100. The scheduler calculates the weight of each node that meets the preferred rule based on an algorithm, and schedules the pod to the node with the highest weight. |
        |Pod Anti Affinity|Pod anti-affinity rules specify that pods are not scheduled to topology domains where pods with matching labels are deployed. Pod anti-affinity rules apply to the following scenarios:

        -   Schedule the pods of an application to different topology domains, such as multiple hosts. This allows you to enhance the stability of the service.
        -   Grant a pod exclusive access to a node. This enables resource isolation and ensures that no other pod can share the specified node.
        -   To ensure that these pods do not interfere with each other, we recommend that you schedule the pods of an application to different hosts.
**Note:** The parameters of pod anti-affinity rules are the same as those of pod affinity rules. You can create the rules for different scenarios. |
        |Toleration|Set toleration rules to allow pods to be scheduled to nodes with matching taints.|
        |Schedule to Virtual Nodes|Specify whether to schedule pods to virtual nodes. This option is unavailable if the cluster does not contain a virtual node.|

    -   Labels and annotations
        -   Pod Labels: Add labels to the pod.
        -   Pod Annotations: Add annotations to the pod.
11. Click **Create**.

12. After the application is created, you are directed to the Complete page. You can find the resource objects for the application and click **View Details** to view application details.

    The details page of the created StatefulSet appears.

13. In the upper-left corner of the page, click **Back** to return to the StatefulSets tab. On the StatefulSets tab, you can find the created application.

14. Click **Scale** in the Actions column to scale the application.

    1.  In the dialog box that appears, set Desired Number of Pods to 3 and click OK. After you scale out the application, you can find that all pods are listed in ascending order of ordinal indexes. If you scale in the application, pods are deleted in descending order of ordinal indexes. This ensures that all pods follow a specific order.

        ![Scale the application](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9045359951/p12438.png)

    2.  In the left-side navigation pane, click Persistent Volumes. On the **Persistent Volume Claims** \> **Persistent Volumes** tab, you can find that a PV and a PVC are created for the newly added pod. However, if the application is scaled in, existing PVs and PVCs are not deleted.


## Related operations

In the left-side navigation pane of the ACK console, click **Clusters**. On the Clusters page, click the name of the cluster or click **Applications** in the **Actions** column. In the left-side navigation pane, click **Workload**. Click the **StatefulSets** tab. On the StatefulSets tab, click the name of the application or click **Details** in the **Actions** column. On the details page, you can edit, scale, redploy, and refresh the application. You can also view the YAML file of the application.

-   Edit: On the details page of the application, click **Edit** in the Actions column of the application to modify the configurations of the application.
-   Scale: On the details page of the application, click **Scale** in the Actions column of the application to scale the application to a required number of pods.
-   View the YAML file: On the details page of the application, click **View in YAML** in the Actions column of the application to update, and download the YAML file of the application. You can also save the YAML file as a template.
-   Redeploy: On the details page of the application, click **Redeploy** in the Actions column of the application to redeploy the application.
-   Refresh: On the details page of the application, click **Refresh** to refresh the application information.

Log on to a master node and perform the following steps to test the persistent storage.

1.  Create a test file in the cloud disk that is mounted to pod nginx-1:

    ```
    kubectl exec nginx-1 ls /tmp            # Query files in the /tmp directory.
    
    kubectl exec nginx-1 touch /tmp/statefulset         # Create a file named statefulset.
    
    kubectl exec nginx-1 ls /tmp
    lost+found
    statefulset
    ```

2.  Delete pod nginx-1:

    ```
    kubectl delete pod nginx-1
    pod"nginx-1" deleted
    ```

3.  After the system recreates and starts pod nginx-1, query the files in the /tmp directory. The following result shows that the statefulset file still exists. This ensures the high availability of the application that runs on the StatefulSet.

    ```
    kubectl exec nginx-1 ls /tmp                         # Query files in the /tmp directory.
    statefulset
    ```



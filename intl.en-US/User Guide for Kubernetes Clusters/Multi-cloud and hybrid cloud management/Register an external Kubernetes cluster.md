# Register an external Kubernetes cluster

This topic describes how to register and manage an external Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console.

-   Resource Access Management \(RAM\) is activated in the [RAM console](https://ram.console.aliyun.com/). Auto Scaling \(ESS\) is activated in the [ESS console](https://essnew.console.aliyun.com).

-   You cannot perform the following operations in the ACK console to modify a registered external Kubernetes cluster: add or remove nodes, upgrade the Kubernetes version, or modify Kubernetes components.

1.  Create a cluster registration proxy.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

    4.  On the Register Cluster tab, set the parameters.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length. It can contain digits, letters, and hyphens \(-\). |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed.

![Resource Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Region**|Select the region where you want to create the cluster. |
        |**Zone**|Select a zone to deploy the cluster.|
        |**VPC**|You can select a virtual private cloud \(VPC\) and a vSwitch from the drop-down lists.|
        |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the API server. You can modify the specifications of the SLB instance. For more information, see [Specification](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the API server. |
        |**EIP**|Specify whether to bind an elastic IP address \(EIP\) to the cluster. If you select this check box, an EIP is automatically created and bound to the cluster.|
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |
        |**Log Service**|Specify whether to activate Log Service. You can select an existing Log Service project or create a Log Service project.If you select **Enable Log Service**, the Log Service plug-in is automatically installed in the cluster. For more information about how to set up Log Service when you deploy an application, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md). |
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling the API. This protects the cluster from being accidentally deleted.|
        |**Labels**|Add labels to the nodes in the cluster. Enter keys and values, and then click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must not exceed 64 characters in length, and cannot start with aliyun, acs:, http://, or https://.
        -   Keys are not case-sensitive. A key must not exceed 128 characters in length, and cannot start with aliyun, acs:, http://, or https://.
        -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
        -   You can add up to 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove unused labels for the remaining labels to take effect. |
        |**Terms of Service**|Read and select *Terms of Service* and *Disclaimer*|

    5.  Click **Create Cluster** on the right side of the page to deploy the cluster.

        You can find the newly created cluster on the Clusters page.

        ![Clusters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2465359951/p48728.png)

2.  Register an external Kubernetes cluster.

    1.  On the Clusters page, find the newly created cluster and click **Details** in the **Actions** column. In this example, click cluster **test-external-cluster1**.

    2.  On the **details** page of the cluster, click the **Connection Information** tab.

    3.  In the **Agent for Connecting to the Cluster** section, click the **Public Network** or **Internal Network** tab based on requirements, and then click **Copy** on the right side. Create a YAML file and paste the copied code into the file. Then, use kubectl to execute the file and register the external cluster.

        For example, you can create an agent.yaml file and paste the copied code into the agent.yaml file. Then, run the `kubectl apply -f agent.yaml` command on an external cluster to register the cluster.

        ![Registration settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2465359951/p48732.png)

    4.  Run the `kubectl get all -n kube-system` command on the external cluster to query the agent status.

        The following output is returned:

        ```
        NAME                                     READY   STATUS    RESTARTS   AGE
        pod/ack-cluster-agent-655b75c987-dwp6b   1/1     Running   0          9s
        
        NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/ack-cluster-agent   1         1         1            1           26m
        
        NAME                                           DESIRED   CURRENT   READY   AGE
        replicaset.apps/ack-cluster-agent-655b75c987   1         1         1       26m
        ```

        After the external cluster is registered, go to the Clusters page. On the Clusters page, you can find that the cluster is in the **Running** state.


On the Clusters page, find cluster **test-external-cluster1** and click **Details** in the **Actions** column. On the details page of the cluster, click the **Basic Information** tab to view basic information about the cluster and click the **Connection Information** tab to view information about how to connect to the cluster.

Run the `kubectl get node` command to query information about the nodes in the **test-external-cluster1** cluster. You can use kubeconfig to connect to the registered cluster and deploy applications in the cluster. For more information, see [Use kubectl to connect to a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

On the details page of the cluster, click **Releases** in the left-side navigation pane. On the **Helm** tab, you can use Helm to release and manage applications in the registered cluster.

**Related topics**  


[Introduction to registered external clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Introduction to registered external clusters.md)


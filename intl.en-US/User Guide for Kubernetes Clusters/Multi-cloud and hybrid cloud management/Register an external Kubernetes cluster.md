# Register an external Kubernetes cluster

This topic describes how to register and manage an external Kubernetes cluster in the Container Service for Kubernetes \(ACK\) console.

-   Resource Access Management \(RAM\) is activated in the [RAM console](https://ram.console.aliyun.com/). Auto Scaling \(ESS\) is activated in the [ESS console](https://essnew.console.aliyun.com).

-   You cannot perform the following operations in the ACK console to modify a registered external kubernetes cluster: add or remove nodes, upgrade the Kubernetes version, or modify Kubernetes component parameters.

1.  Create a cluster registration proxy.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  In the upper-right corner of the Clusters page, click **Register Cluster**.

    4.  On the Create Cluster Registration Proxy tab, set the parameters.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length. It can contain digits, letters, and hyphens \(-\). |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed.

![Resource Group](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Region**|Select the region where you want to create the cluster. |
        |**Zone**|Select a zone to deploy the cluster.|
        |**VPC**|You can select a virtual private cloud \(VPC\) and a VSwitch from the drop-down lists.|
        |**EIP**|Specify whether to bind an elastic IP address \(EIP\) to the cluster. If you select this check box, an EIP is automatically created and bound to the cluster.|
        |**Log Service**|Specify whether to activate Log Service. You can select an existing Log Service project or create a Log Service project.If you select **Enable Log Service**, the Log Service plug-in is automatically installed in the cluster. For more information about how to set up Log Service when you create an application, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md). |
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling the API. This avoids user errors.|
        |**Labels**|Attach labels to nodes. Enter keys and values, and click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
        -   Values are not case-sensitive. A value must be 1 to 128 characters in length, and cannot start with http:// or https://.
        -   The keys of labels that are attached to the same resource must be unique. If you add a label with a used key, the label overwrites the one using the same key.
        -   You can attach up to 20 labels to each resource. If you attach more than 20 labels to a resource, all labels become invalid. You must detach unused labels for the remaining labels to take effect. |
        |**Terms of Service**|Read and select *Terms of Service* and *Disclaimer*|

    5.  Click **Create Cluster** on the right side of the page.

        You can find the newly created cluster on the Clusters page.

        ![Clusters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2465359951/p48728.png)

2.  Register an external Kubernetes cluster.

    1.  On the Clusters page, find the newly created cluster and click **Details** in the **Actions** column. In this example, click cluster **test-external-cluster1**.

    2.  On the **details** page of the cluster, click the **Connection Information** tab.

    3.  In the **Agent for Connecting to the Cluster** section, click the **Public Network** tab or **Internal Network** tab based on requirements, and then click **Copy**. Create a YAML file and paste the copied code into the file. Then, use kubectl to execute the file and register the external cluster.

        For example, you can create an agent.yaml file and paste the copied code into the agent.yaml file. Then, run the `kubectl apply -f agent.yaml` command on an external cluster to perform registration.

        ![Registration settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2465359951/p48732.png)

    4.  Run the `kubectl get all -n kube-system` command on the external cluster to query the agent status.

        The following command output is returned:

        ```
        NAME                                     READY   STATUS    RESTARTS   AGE
        pod/ack-cluster-agent-655b75c987-dwp6b   1/1     Running   0          9s
        
        NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
        deployment.apps/ack-cluster-agent   1         1         1            1           26m
        
        NAME                                           DESIRED   CURRENT   READY   AGE
        replicaset.apps/ack-cluster-agent-655b75c987   1         1         1       26m
        ```

        After the external cluster is registered, go to the Clusters page. On the Clusters page, you can find that the cluster is in the **Running** state.


On the Clusters page, find cluster **test-external-cluster1** and click **Details** in the **Actions** column to view **basic information** and **connection information** about the cluster.

Run the `kubectl get node` command to query information about nodes in the cluster **test-external-cluster1**. You can use kubeconfig to connect to the registered cluster and deploy applications in the cluster. For more information, see [Use kubectl to connect to a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

In the left-side navigation pane of the details page of the cluster, click **Releases**. On the **Helm** tab, you can use Helm to manage released applications.

**Related topics**  


[Introduction to registered external clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Introduction to registered external clusters.md)


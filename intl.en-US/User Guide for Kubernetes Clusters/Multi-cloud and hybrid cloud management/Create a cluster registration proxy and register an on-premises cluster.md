---
keyword: [register a cluster, connect to a data center]
---

# Create a cluster registration proxy and register an on-premises cluster

Container Service for Kubernetes \(ACK\) allows you to create a cluster registration proxy and use the proxy to register a Kubernetes cluster that is deployed in a data center or on a third-party cloud. This allows you to manage your clusters in the ACK console in a centralized manner. This topic describes how to create a cluster registration proxy and register a Kubernetes cluster that is deployed in a data center.

Resource Access Management \(RAM\) is activated in the [RAM console](https://ram.console.aliyun.com/). Auto Scaling \(ESS\) is activated in the [ESS console](https://essnew.console.aliyun.com).

1.  Create a cluster registration proxy.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, click **Clusters**.

    3.  On the Clusters page, click **Create Kubernetes Cluster** in the upper-right corner of the page.

    4.  On the Register Cluster tab, set the parameters.

        |Parameter|Description|
        |---------|-----------|
        |**Cluster Name**|Enter a name for the ACK cluster.

**Note:** The name must be 1 to 63 characters in length, and can contain digits, letters, and hyphens \(-\). |
        |**Resource Group**|Move the pointer over **All Resources** at the top of the page and select the resource group to which the cluster belongs. The name of the selected resource group is displayed, as shown in the following figure.

![Resource Groups](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9688404061/p127165.png) |
        |**Region**|Select a region to deploy the cluster. |
        |**VPC**|Select a virtual private cloud \(VPC\) from the drop-down list.|
        |**VSwitch**|Select a node vSwitch that belongs to the VPC that you selected.|
        |**Network Plug-in**|If you select **Disable**, node pools are not used to scale computing resources. In this case, network plug-ins are not required.|
        |If you select **Terway** and **IPVLAN**, IPVLAN and eBPF are combined to implement NIC virtualization and sharing. In this case, you must use the Alibaba Cloud Linux 2 operating system.|
        |**Pod VSwitch**|Select a pod vSwitch that belongs to the VPC that you selected.|
        |**Service CIDR**|Use the default value 172.21.0.0/20.|
        |**Access to API Server**|By default, an internal-facing Server Load Balancer \(SLB\) instance is created for the cluster API server. You can modify the specifications of the SLB instance. For more information, see [Instance types and specifications](/intl.en-US/Classic Load Balancer/User Guide/Instance/SLB instance overview.md).

**Note:** If you delete the SLB instance, you cannot access the API server. |
        |**EIP**|Specify whether to associate an elastic IP address \(EIP\) with the cluster. If you select this check box, an EIP is automatically created and associated with the cluster.|
        |**Security Group**|You can select **Create Basic Security Group**, **Create Advanced Security Group**, or **Select Existing Security Group**. For more information, see [Overview](/intl.en-US/Security/Security groups/Overview.md). |
        |**Log Service**|Specify whether to enable Log Service. You can select an existing Log Service project or create a new Log Service project.If you select **Enable Log Service**, the Log Service plug-in is automatically installed in the cluster. You can use Log Service with simple configurations when you create an application. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md). |
        |**Deletion Protection**|Specify whether to enable deletion protection. If you select this check box, the cluster cannot be deleted in the console or by calling an API operation. This avoids cluster deletion due to user errors.|
        |**Labels**|Add labels to nodes. Enter a key and a value, and then click **Add**.

**Note:**

        -   Key is required. Value is optional.
        -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, acs:, http://, or https://.
        -   Values are not case-sensitive. A value must be 1 to 128 characters in length, and cannot start with aliyun, acs:, http://, or https://. The Value parameter can be left empty.
        -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the label that uses the same key.
        -   You can add up to 20 labels to a resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove excess labels for the remaining labels to take effect. |
        |**Terms of Service**|Read and select *Terms of Service* and *Disclaimer*.|

    5.  On the right side of the page, click **Create Cluster**.

        You can find the newly created cluster on the Clusters page.

        ![Clusters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2465359951/p48728.png)

2.  Register an on-premises cluster.

    1.  On the Clusters page, select the newly created cluster and click **Details** in the **Actions** column. In this example, select cluster **test-external-cluster1**.

    2.  On the **details** page of the cluster, click the **Connection Information** tab.

    3.  In the **Agent for Connecting to the Cluster** section, click the **Public Network** or **Internal Network** tab based on your business requirements and then click **Copy** on the right side. Create a YAML file and paste the copied code to the file. Then, use kubectl to execute the file and register the external cluster.

        For example, you can create an agent.yaml file and paste the copied code to the agent.yaml file. Then, run the `kubectl apply -f agent.yaml` command in the on-premises cluster to register the cluster.

        ![Registration settings](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2465359951/p48732.png)

    4.  Run the following command in the on-premises cluster to query the state of the agent:

        ```
        kubectl -n kube-system get pod |grep ack-cluster-agent
        ```

        The following output is returned:

        ```
        ack-cluster-agent-5f7d568f6-6fc4k              1/1     Running   0          9s
        ack-cluster-agent-5f7d568f6-tf6fp              1/1     Running   0          9s
        ```

        After the cluster is registered, verify that the cluster is in the **Running** state on the Clusters page.


On the Clusters page, find the newly registered cluster and click **Details** in the **Actions** column. On the page that appears, you can view **Basic Information** and **Connection Information** about the cluster.

You can use kubeconfig to connect to the cluster and deploy applications in the cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview of registered clusters.md)


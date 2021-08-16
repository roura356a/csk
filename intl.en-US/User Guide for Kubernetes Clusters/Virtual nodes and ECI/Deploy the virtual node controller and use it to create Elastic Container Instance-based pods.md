---
keyword: [virtual nodes, ack-virtual-node, create Elastic Container Instance-based pods]
---

# Deploy the virtual node controller and use it to create Elastic Container Instance-based pods

Virtual nodes enable seamless integration between Kubernetes and Elastic Container Instance. Virtual nodes empower Kubernetes clusters with high elasticity. This way, Kubernetes clusters are no longer limited by the computing capacity of cluster nodes. You can create Elastic Container Instance-based pods as needed to avoid the planning of cluster capacity. This topic describes virtual nodes and elastic container instances. It also describes how to deploy the virtual node controller \(ack-virtual-node\) and use it to create Elastic Container Instance-based pods.

-   A managed Kubernetes cluster or a dedicated Kubernetes cluster that uses a Kubernetes version later than 1.14 is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md) or [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   Elastic Container Instance is activated. You can log on to the [Elastic Container Instance console](https://eci.console.aliyun.com/) to activate the service.
-   The region where the cluster is deployed is supported by Elastic Container Instance. To view the supported regions and zones, log on to the [Elastic Container Instance console](https://eci.console.aliyun.com/).

## Virtual nodes and elastic container instances

[Elastic Container Instance](https://www.alibabacloud.com/zh/products/elastic-container-instance) is a serverless compute service that is provided by Alibaba Cloud for containerization. You can use elastic container instances to set up an O&M-free and isolated runtime environment for your containers. Elastic container instances allow you to focus on containerized applications without the need to purchase or manage Elastic Compute Service \(ECS\) instances. This way, you do not need to perform infrastructure maintenance. You can create elastic container instances to meet your business requirements. You are charged for resource usage on a per second basis.

Virtual nodes can drastically reduce computing costs and improve scaling efficiency. Virtual nodes are suitable in the following scenarios:

-   Online business that requires elastic scaling to withstand traffic fluctuations, such as online education and e-commerce. Virtual nodes optimize the maintenance of resource pools. This helps you reduce computing costs.
-   Virtual nodes can reduce costs in computing scenarios where Spark or Presto is used to process data.
-   Continuous integration and continuous delivery \(CI/CD\) pipeline: Jenkins and GitLab Runner.
-   Jobs: Jobs in AI computing scenarios and CronJobs.

Container Service for Kubernetes \(ACK\) provides multiple serverless container services based on virtual nodes and elastic container instances. These services include serverless Kubernetes \(ASK\) and ACK on Elastic Container Instance. You can use these services to deploy elastic and maintenance-free workloads.

![virtual node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9265359951/p85192.png)

## Step 1: Deploy ack-virtual-node in ACK clusters

**Note:**

-   The virtual node controller ack-virtual-node is managed by ASK clusters. Therefore, you can create Elastic Container Instance-based pods in ASK clusters without the need to deploy ack-virtual-node. In managed or dedicated Kubernetes clusters, you must deploy ack-virtual-node before you can create Elastic Container Instance-based pods.
-   In managed Kubernetes clusters, you can deploy ack-virtual-node on the **Add-ons** page of the console. By default, ack-virtual-node is managed by the clusters after it is deployed.
-   In dedicated Kubernetes clusters, you can deploy ack-virtual-node on the **App Catalog** page of the console. By default, ack-virtual-node is managed by the clusters after it is deployed.

**Managed Kubernetes clusters**

Perform the following steps to deploy ack-virtual-node in a managed Kubernetes cluster:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Operations** \> **Add-ons**.

5.  On the **Add-ons** page, select **ack-virtual-node** and click **Install**.

    The default vSwitch and security group of the cluster are used for elastic container instances that are deployed by ack-virtual-node. If you want to modify these settings, see [What to do next](#section_9ec_223_45i).

    ![Install ack-virtual-node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0263548161/p232042.png)


**Dedicated Kubernetes Clusters**

Perform the following steps to deploy ack-virtual-node in a dedicated Kubernetes cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab, and find and click **ack-virtual-node**.

    A large number of applications are displayed on the **Alibaba Cloud Apps** tab. In the upper-left corner of the **App Catalog** page, you can enter **ack-virtual-node** in the **Name** search box and click the search icon to search for the application. You can also enter a keyword to perform a fuzzy match.

4.  On the App Catalog - ack-virtual-node page, select the cluster where you want to deploy ack-virtual-node in the Deploy section.

    **Namespace** is automatically set to kube-system. **Release Name** is automatically set to ack-virtual-node.

5.  On the App Catalog - ack-virtual-node page, click the **Parameters** tab and set the parameters.

    |Parameter|Description|How to obtain the value|
    |---------|-----------|-----------------------|
    |ALIYUN\_CLUSTERID|The ID of the cluster where you want to deploy ack-virtual-node|Go to the details page of the cluster. Click the Basic Information tab. In the **Basic Information** section, you can view the **cluster ID**.|
    |ALIYUN\_RESOURCEGROUP\_ID|The ID of the resource group to which the cluster belongs|If you do not specify this parameter, the default resource group is used. To specify a resource group, log on to the [Resource Management console](https://resourcemanager.console.aliyun.com/) to obtain the **ID of the resource group** that you want to use.|
    |ECI\_REGION|The name of the region where the cluster is deployed|Go to the details page of the cluster. Click the Basic Information tab. In the **Basic Information** section, you can view the **region** where the cluster is deployed. **Note:** For example, cn-hangzhou indicates the China \(Hangzhou\) region. |
    |ECI\_VSWITCH|vSwitches|On the Nodes page, click the ID of a node. On the **instance details** page, you can obtain the value of VSwtich in the **Configuration Information** section. **Note:**

Make sure that the zone of the vSwitch is supported by Elastic Container Instance.

You can specify vSwitches that are deployed in different zones. Multiple vSwitches can be specified in the following format: `ECI_VSWITCH: "vsw-xxxxxxx1, vsw-xxxxxxx2, vsw-xxxxxxx3"`. |
    |ECI\_SECURITY\_GROUP|The ID of the security group to which nodes in the cluster belong|On the Nodes page, click the ID of a node. On the instance details page, click the **Security Groups** tab. Then, click the **Security Groups** tab to obtain the security group ID.|
    |ECI\_ACCESS\_KEY|The AccessKey ID of your account|For more information, see [Obtain an AccessKey pair](). You must attach the **AliyunECIFullAccess** policy to your account in the Resource Access Management \(RAM\) console. For more information, see [Grant permissions to a RAM user](). |
    |ECI\_SECRET\_KEY|The AccessKey secret of your account|For more information, see [Obtain an AccessKey pair](). You must attach the **AliyunECIFullAccess** policy to your account in the RAM console. For more information, see [Grant permissions to a RAM user](). |

6.  On the App Catalog - ack-virtual-node page, select the cluster where you want to deploy ack-virtual-node in the Deploy section.

    **Namespace** is automatically set to kube-system. **Release Name** is automatically set to ack-virtual-node. Click **Create**.

    ![Deploy ack-virtual-node](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9265359951/p47256.png)

7.  Check the deployment status of **virtual-node-controller**.

    For more information, see [Use kubectl on Cloud Shell to manage ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl on Cloud Shell to manage ACK clusters.md).

    Run the following command to query the deployment status of **virtual-node-controller**:

    ```
    kubectl -n kube-system get deploy ack-virtual-node-controller
    ```

    Expected output:

    ```
    NAME                     READY   AGE
    virtual-node-controller  1/1     1m
    ```


## Step 2: Create Elastic Container Instance-based pods

**Note:** All pods in ASK clusters are deployed on elastic container instances. You do not need to use labels to identify these pods. Therefore, the following operations do not apply to ASK clusters. The following operations show how to create an Elastic Container Instance-based pod in an ACK cluster.

You can use the following methods to create an Elastic Container Instance-based pod in an ACK cluster.

-   **Add a label to the pod**.

    Add the `alibabacloud.com/eci=true` label to the pod that you want to create. Then, an Elastic Container Instance-based pod is created and scheduled to a virtual node. Examples:

    1.  Run the following command to add a label to the pod:

        ```
        kubectl run nginx --image nginx -l alibabacloud.com/eci=true
        ```

    2.  Run the following command to query the pod:

        ```
        kubectl get pod -o wide|grep virtual-kubelet
        ```

        Expected output:

        ```
        nginx-7fc9f746b6-r4xgx     0/1     ContainerCreating   0          20s   192.168.XX.XX   virtual-kubelet        <none>           <none>
        ```

-   **Add a label to the namespace to which the pod belongs**.

    Add the `alibabacloud.com/eci=true` label to the namespace to which the pod belongs. Then, an Elastic Container Instance-based pod is created and scheduled to a virtual node. Example:

    1.  Run the following command to create a virtual node:

        ```
        kubectl create ns vk
        ```

    2.  Run the following command to add a label to the namespace to which the pod belongs:

        ```
        kubectl label namespace vk alibabacloud.com/eci=true
        ```

    3.  Run the following command to schedule the pod to a virtual node:

        ```
        kubectl -n vk run nginx --image nginx
        ```

    4.  Run the following command to query the pod:

        ```
        kubectl -n vk get pod -o wide|grep virtual-kubelet
        ```

        Expected output:

        ```
        nginx-6f489b847d-vgj4d      1/1     Running             0          1m   192.168.XX.XX   virtual-kubelet        <none>           <none>
        ```


## What to do next

**Note:** The following operations apply only to ACK clusters and do not apply to ASK clusters.

**Delete virtual nodes in ACK clusters**

1.  Uninstall ack-virtual-node.

-   After you delete all Elastic Container Instance-based pods in a managed Kubernetes cluster, uninstall ack-virtual-node on the Add-ons page.

-   After you delete all Elastic Container Instance-based pods in a dedicated Kubernetes cluster, delete ack-virtual-node on the Helm page.

2.  Run the `kubectl delete node <node name>` to delete the virtual nodes that you want to delete.

    **Note:** If you do not delete the Elastic Container Instance-based pods in the cluster before you uninstall ack-virtual-node, the elastic container instances are retained in the cluster.


**Related topics**  


[Use an elastic container instance to run a Job](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Use an elastic container instance to run a Job.md)

[Use elastic container instances in ASK clusters]()


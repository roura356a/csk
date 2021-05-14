---
keyword: [CCM, multiple route tables of a VPC, dedicated ACK clusters, K8s]
---

# Configure multiple route tables for a VPC

Container Service for Kubernetes \(ACK\) uses Cloud Controller Manager \(CCM\) to add route entries to the route table of the virtual private cloud \(VPC\) where the cluster is deployed. This enables network connections between pods in the cluster. You can update the cloud-config file to configure multiple route tables for the VPC where a dedicated ACK cluster is deployed. This topic describes how to configure multiple route tables for the VPC where a dedicated ACK cluster is deployed.

-   A dedicated ACK cluster is created. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   The Flannel network plug-in is used in the dedicated ACK cluster.
-   The CCM version is later than v1.9.3.86-g4454991-aliyun. For more information about how to check the CCM version, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md).

When multiple route tables are configured for a VPC, you can select to associate a route table to Elastic Compute Service \(ECS\) instances. Previous versions of CCM allow you to configure only one route table for a VPC. Therefore, you must upgrade the CCM component to the latest version in the ACK console. For more information about how to upgrade the CCM version, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md).

**Note:** Only a dedicated ACK cluster allows you to configure multiple route tables for the VPC where the cluster is deployed. To configure multiple route tables for the VPC where a managed ACK cluster is deployed, [Submit a ticket](https://workorder-intl.console.aliyun.com/console.htm).

## Configure multiple route tables for the VPC by using a kubectl client

To use a kubectl client to configure multiple route tables for the VPC, make sure that the kubectl client is connected to the dedicated ACK cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

1.  Update the cloud-config file.

    Run the `kubectl edit cm -n kube-system cloud-config` command to edit the cloud-config file. Replace `${ROUTE_TABLES_IDS}` with the IDs of route tables in the VPC. Make sure that the ID of the system route table is included. Separate multiple route table IDs with commas \(,\). For example, vtb-t4n788888\*\*\*\*,vtb-t4n7k6u3m0n840799\*\*\*\*.

    The following cloud-config file is provided as an example.

    ```
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: cloud-config
      namespace: kube-system
    data:
      cloud-config.conf: |-
        {
            "Global": {
                "routeTableIDs": "${ROUTE_TABLES_IDS}"
            }
        }
    ```

2.  Run the following command to restart the pod that runs CCM.

    ```
    kubectl -n kube-system delete po -lapp=cloud-controller-manager
    ```

    After the pod is restarted, verify that route entries of the cluster nodes are displayed in the specified route tables.


## Configure multiple route tables for the VPC by using the ACK console

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  Select the kube-system namespace. Find the cloud-config ConfigMap and click **Edit YAML** in the Actions column.

6.  In the **View in YAML** panel, set routeTableIDs to the IDs of route tables in the VPC. Make sure that the ID of the system route table is included. Separate multiple route table IDs with commas \(,\). For example, vtb-t4n788888\*\*\*\*,vtb-t4n7k6u3m0n840799\*\*\*\*. Then, click **OK**.

    ![cloud-config](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3391790261/p248985.png)

7.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

8.  In the kube-system namespace, find the alicloud-application-controller Deployment and choose **More** \> **Redeploy** in the Actions column.

    After the redeployment is completed, verify that route entries of the cluster nodes are displayed in the specified route tables.



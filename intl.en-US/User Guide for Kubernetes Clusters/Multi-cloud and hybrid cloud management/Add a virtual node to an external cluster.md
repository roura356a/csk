---
keyword: [external Kubernetes cluster, virtual node, elastic workload, ack-virtual-node]
---

# Add a virtual node to an external cluster

This topic describes how to add a virtual node to an external Kubernetes cluster that is registered in Container Service for Kubernetes \(ACK\). After you perform this operation, you can use the virtual node to access pods on an Elastic Container Instance \(ECI\).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab, and find and click **ack-virtual-node**.

    In the upper right corner of the **App Catalog** page, you can enter **ack-virtual-node** into the Name search bar and click the search icon. You can also enter a keyword to perform a fuzzy match.

4.  On the App Catalog - ack-virtual-node page, select an external cluster in the **Deploy** section to deploy the application.

    **Namespace** and **Release Name** are automatically set to the default values and cannot be changed.

5.  On the App Catalog - ack-virtual-node page, click the **Parameters** tab, set the parameters, and then click **Create** in the **Deploy** section.

    ![vn](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6465359951/p102320.png)

    |Parameter|Description|
    |---------|-----------|
    |ALIYUN\_CLUSTERID|The ID of the external cluster. The cluster ID is automatically provided.|
    |ALIYUN\_RESOURCEGROUP\_ID|The ID of the resource group. This parameter is optional.|
    |ECI\_REGION|The ID of the region where the ECI is deployed. The region ID is automatically provided.|
    |ECI\_VSWITCH|The ID of the VSwitch that is connected to the pod that runs on the ECI.|
    |ECI\_SECURITY\_GROUP|The ID of the security group to which the pod that runs on the ECI belongs.|
    |ECI\_ACCESS\_KEY|The AccessKey ID. It is a credential used by the virtual node to access the ECI.|
    |ECI\_SECRET\_KEY|The AccessKey secret. It is a credential used by the virtual node to access the ECI.|

    **Note:** If the external cluster is registered through a public network, you must delete vpc in the registry address of the image on the Parameters tab. For example, after you delete vpc, the value of the registry address is registry.cn-hangzhou.aliyuncs.com/acs/virtual-nodes-eci.


**Related topics**  


[Overview of registered clusters in the ACK console](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Overview of registered clusters in the ACK console.md)

[Deploy ack-virtual-node in an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy ack-virtual-node in an ACK cluster.md)


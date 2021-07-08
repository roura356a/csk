---
keyword: [expand a cluster that runs sandboxed containers, worker nodes]
---

# Expand a cluster that runs sandboxed containers

This topic describes how to scale out the number of worker nodes in a Container Service for Kubernetes \(ACK\) cluster that runs sandboxed containers by using the ACK console.

You cannot scale out the number of master nodes in a cluster that runs sandboxed containers.

When you expand a cluster that runs sandboxed containers, you must set the parameters as required in the following table. Otherwise, the added nodes cannot run sandboxed containers.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to expand and choose **More** \> **Expand** in the **Actions** column.

4.  On the Node Pools page, select the node pool that you want to scale out and click **Scale Out** in the **Actions** column.

5.  In the dialog box that appears, set the parameters and click **Submit**.

    In this example, the number of worker nodes in the cluster is increased from three to five. The following table describes the required parameters.

    |Parameter|Description|
    |---------|-----------|
    |Nodes to Add|The number of nodes to be added to the cluster.|
    |Region|By default, the region where the cluster is deployed is displayed.|
    |Container Runtime|By default, Sandboxed-Container is displayed.|
    |VPC|Set the network for the nodes. You can select a virtual private cloud \(VPC\) from the drop-down list. If no VPC is available, click **Create VPC** to create one. For more information, see [Create a VPC](/intl.en-US/VPCs and vSwitchs/Work with VPCs.md).|
    |VSwitch|Select one or more vSwitches. You can select up to three vSwitches deployed in different **zones**. If no vSwitch is available, click **Create VSwitch** to create one. For more information, see [Create a vSwitch](/intl.en-US/VPCs and vSwitchs/Work with vSwitches.md). |
    |Billing Method|ACK supports the **pay-as-you-go** and **subscription** billing methods. If you select the **subscription** billing method,     -   you must set the **duration**. You can select 1, 2, 3, or 6 months. If you require a longer duration, you can select 1 to 5 years.
    -   you must specify whether to enable **Auto Renewal**. |
    |Instance Type|Select **ECS Bare Metal Instance**.|
    |Selected Types|The selected instance types.|
    |System Disk|Standard SSDs, enhanced SSDs \(ESSDs\), and ultra disks are supported.|
    |Mount Data Disk|Standard SSDs, ESSDs, and ultra disks are supported. **Note:** You can specify whether to encrypt the mounted data disks. The disks are used to store the root file systems of containers on the nodes. Therefore, you must mount a data disk of at least 200 GiB. We recommend that you mount a data disk of at least 1 TB. |
    |Logon Type|    -   Key pair:

When you created the cluster, you selected Key Pair as the logon method. If no key pair is available, click **create a key pair** to create one in the Elastic Compute Service \(ECS\) console. For more information, see [Create an SSH key pair](/intl.en-US/Security/Key pairs/Use an SSH key pair/Create an SSH key pair.md). After the key pair is created, set it as the credentials to log on to the cluster.

    -   Password:
        -   **Password**: Enter the password that is used to log on to the nodes.
        -   **Confirm Password**: Enter the password again. |
    |Key Pair|
    |Public IP|If you select **Assign a Public IPv4 Address to Each Node**, public IPv4 addresses are automatically assigned to the new nodes. You can connect to the nodes through the assigned IPv4 addresses. For more information about public IP addresses, see [Elastic IP Addresses](/intl.en-US/Network/Instance IP addresses/Elastic IP Addresses.md).|
    |ECS Label|Attach labels to the added ECS instances. **Note:**

    -   Key is required. Value is optional.
    -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
    -   Values are not case-sensitive. A value must be 1 to 128 characters in length, and cannot start with http:// or https://.
    -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
    -   You can add at most 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove excess labels for the remaining labels to take effect. |
    |Node Label|Add labels to the added nodes. **Note:**

    -   Key is required. Value is optional.
    -   Keys are not case-sensitive. A key must be 1 to 64 characters in length, and cannot start with aliyun, http://, or https://.
    -   Values are not case-sensitive. A value must be 1 to 128 characters in length, and cannot start with http:// or https://.
    -   The keys of labels that are added to the same resource must be unique. If you add a label with a used key, the label overwrites the one that uses the same key.
    -   You can add at most 20 labels to each resource. If you add more than 20 labels to a resource, all labels become invalid. You must remove excess labels for the remaining labels to take effect. |
    |Resource Group|Specify the resource group of the new nodes.|
    |Taints|Add taints to the added nodes.|
    |RDS Whitelist|Set the RDS whitelist. This operation adds the IP addresses of the nodes to the RDS whitelist.|
    |CloudMonitor Agent|You can install the CloudMonitor agent on the nodes and view monitoring information about the nodes in the CloudMonitor console.|
    |User Data|For more information, see [Overview of ECS instance user data](/intl.en-US/Instance/Manage instance configurations/Manage instance user data/Overview of ECS instance user data.md).|


After the nodes are added, go to the details page of the cluster. In the left-side navigation pane, click **Node Pools**. Verify that the number of worker nodes is increased from three to five.


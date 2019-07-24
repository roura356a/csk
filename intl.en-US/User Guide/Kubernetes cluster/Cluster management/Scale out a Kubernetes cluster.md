# Scale out a Kubernetes cluster {#concept_avm_wrh_1hb .concept}

This topic describes how to use the Container Service console to scale out a Kubernetes cluster. Specifically, this task adds new Worker nodes to the cluster.

## Limits {#section_vgd_yrh_1hb .section}

No Master node can be added to any existing Kubernetes cluster.

## Procedure {#section_wk3_1sh_1hb .section}

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane under **Container Service-Kubernetes**, choose **Clusters** \> **Clusters**.
3.  Find the target cluster. Then, in the **Action** column, click **Expand**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16647/156393259510904_en-US.png)

4.  Set the number of Worker nodes that you want to add to the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16647/156393259510905_en-US.png)

5.  Set the parameters of the Worker nodes.

    -   **Instance Type**: Indicates the ECS instance type. You can select multiple ECS instance types. For more information, see [Instance type families](../../../../reseller.en-US/Instances/Instance type families.md#).
    -   **System Disk**: Indicates the type and capacity of the system disk. Available system disks are SSD disks and Ultra disks.
    -   **Attach Data Disk**: Indicates the type and capacity of the data disk. Available data disks are SSD disks, Ultra disks, and basic disks.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16647/156393259550574_en-US.png)

6.  Set the logon type for the Worker nodes.
    -   Set the key pair.

        1.  Click **Create a new key pair**.

            **Note:** You must use the ECS console to create a key pair. For more information, see [Create an SSH key pair](../../../../reseller.en-US/Security/Key pairs/Use an SSH key pair.md#).

        2.  Select the key pair that you created from the **Key Pair Name** drop-down list.
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15639325969041_en-US.png)

    -   Set the password.
        -   **Logon Password**: Set the node logon password.
        -   **Confirm Password**: Confirm your node logon password.
7.  Add an RDS instance to the RDS whitelist.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/156393259621270_en-US.png)

8.  Attach tags to the cluster.

    Enter a key and its value, and click **Add**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/156393259650326_en-US.png)

9.  Click **Submit**.

## What to do next {#section_jxu_cjm_4f0 .section}

Follow these steps after you complete the procedure to verify results:

1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Nodes**.
2.  Select the target Kubernetes cluster from the **Clusters** drop-down list to verify that the number of Worker nodes is changed.


# Create a persistent storage volume claim {#task_bzy_scf_j2b .task}

You can create a persistent storage volume claim \(PVC\) by using the Container Service console.

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#). 
-   You have created a storage volume. In this example, use a cloud disk to create a cloud storage volume. For more information, see [Use Alibaba Cloud cloud disks](reseller.en-US/User Guide/Kubernetes cluster/Storage/Use Alibaba Cloud cloud disks.md#).

    By default, the storage claim is bound to the storage volume depending on the label alicloud-pvname. When the data volume is created by using the Container Service console, the storage volume is labeled by default. If the storage volume label does not exist, you must add a label before you select to bound this storage volume.


1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs). 
2.  Under Kubernetes, click **Application** \> **Volumes Claim** in the left-side navigation pane to enter the Volumes Claims list page. 
3.  Select the target cluster and namespace, and click **Create** in the upper-right corner. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16691/153958549410715_en-US.png)

4.  Complete the configurations in the Create Volume Claim dialog box, and click **Create**. 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16691/153958549410716_en-US.png)

    -   **Volume claim type**: Consistent with storage volume, including cloud disk, NAS, and OSS types.
    -   **Name**: Enter the storage volume claim name.
    -   **Distribution mode**: Currently, only existing storage volumes are supported.
    -   **Existing storage volume**: Select to bind the storage volume of this type.
    -   **Total**: Claim usage, cannot be greater than the total amount of storage volumes.
    **Note:** If a storage volume already exists in your cluster and is not used, but cannot be found in **Select Existing Storage Volume**, maybe the alicloud-pvname label is not defined.

    If you cannot find an available storage volume, you can click **Clusters** \> **Volumes** in the left-side navigation pane. Find the target storage volume, click **Label Management** on the right. Add the corresponding label alicloud-pvname, the value is the name of the storage volume. The cloud storage volume defaults to the cloud disk ID as the name of the storage volume.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16691/153958549410717_en-US.png)

5.  Return to the Volumes Claims list, you can see that the newly created storage claim appears in the list. 


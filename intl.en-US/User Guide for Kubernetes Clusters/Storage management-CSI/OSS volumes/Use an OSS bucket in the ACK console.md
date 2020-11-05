# Use an OSS bucket in the ACK console

This topic describes how to use an Object Storage Service \(OSS\) bucket in the Container Service for Kubernetes \(ACK\) console.

An OSS bucket is created. For more information, see [Create buckets](/intl.en-US/Quick Start/Create buckets.md).

**Note:** If a node and an OSS bucket are located in the same region, you can use the internal endpoint of the OSS bucket when you mount the bucket to the node.

## Create a PV

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Persistent Volumes**.

5.  Click the **Persistent Volumes** tab. On the **Persistent Volumes** tab, click **Create** in the upper-right corner.

6.  In the **Create PV** dialog box, configure the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PV Type**|You can select Cloud Disk, NAS, or OSS. In this example, select OSS.|
    |**Volume Name**|The name of the persistent volume \(PV\). The name must be unique in the namespace. In this example, enter pv-oss.|
    |**Volume Plug-in**|You can select Flexvolume or CSI. In this example, select CSI.|
    |**Capacity**|The capacity of the PV.|
    |**Access Mode**|Default value: ReadWriteMany.|
    |**AccessKey ID**|The AccessKey pair that is required to access the OSS bucket.|
    |**AccessKey Secret**|
    |**Optional Parameters**|Configure custom parameters for the OSS bucket in the format of `-o *** -o ***`.|
    |**Bucket ID**|The name of the OSS bucket that you want to use. Click **Select Bucket**. In the dialog box that appears, select the OSS bucket that you want to use and click **Select**.|
    |**Endpoint**|Select the endpoint of the OSS bucket.    -   If the OSS bucket and the ECS instance are located in different regions, select **Public Endpoint**.
    -   If the OSS bucket and the ECS instance are located in the same region, set the value based on the network type of the cluster.
        -   If the cluster uses a virtual private cloud \(VPC\), select **VPC Endpoint**.
        -   If the cluster uses the classic network, select **Internal Endpoint**. |
    |**Label**|Attach labels to the PV.|

7.  Click **Create**.


## Create a PVC

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Persistent Volumes**.

5.  On the **Persistent Volume Claims** tab, click **Create** in the upper-right corner.

6.  In the Create PVC dialog box, configure the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PVC Type**|You can select Cloud Disk, NAS, or OSS. In this example, select OSS.|
    |**Name**|The name of the persistent volume claim \(PVC\). The name must be unique in the namespace.|
    |**Allocation Mode**|In this example, select Existing Volumes.**Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**. For information about how to configure PV parameters, see [Create a PV](#section_kuh_z2k_6r2). |
    |**Existing Volumes**|Click **Select PV**. Find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity of the PVC.**Note:** The capacity of the PVC cannot exceed the capacity of the PV. |

7.  Click **Create**.

    After you create the PVC, you can find it on the Persistent Volume Claims tab. In this example, a PVC named csi-oss-pvc is created and a PV is bound to the PVC.


## Create an application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Workload**.

5.  On the **Deployments** tab, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure basic settings.

    ![Basic settings](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7534749951/p10973.png)

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Namespace|The namespace in which the application is deployed. If you do not set this field, the system uses the default namespace.|
    |Replicas|The number of pods in an application. Default value: 2.|
    |Type|The type of the application. You can select **Deployments**, **StatefulSets**, **Jobs**, **Cron Jobs**, and **DaemonSets**.|
    |Label|Add one or more labels to the application.|
    |Annotations|Add one or more annotations to the application.|
    |Synchronize Timezone|Specify whether to synchronize the timezone from nodes to containers.|

    **Note:** In this example, **Deployments** is selected as the application type.

7.  Click **Next**.

8.  Configure the container parameters.

    This example describes how to configure the volume parameters. For more information about other parameters, see [Use an image to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Use an image to create a stateless application.md).

    You can add local volumes and cloud volumes.

    -   **Local storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, specify Mount Source and Container Path to mount a source path to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Cloud storage**: You can add cloud volumes.
    In this example, specify csi-oss-pvc as Mount Source and mount it to the /tmp path in the container.

    ![volume](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6980854061/p179733.png)

9.  Click **Next**.

10. Set other parameters and click **Create**.

    After you create the application, you can use the OSS bucket in the application.


You can also use an OSS bucket by running commands. For more information, see [Provision static OSS volumes by using YAML files](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/OSS volumes/Provision static OSS volumes by using YAML files.md).


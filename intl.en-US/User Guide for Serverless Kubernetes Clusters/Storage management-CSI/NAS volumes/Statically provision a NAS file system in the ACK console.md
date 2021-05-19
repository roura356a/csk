---
keyword: static NAS volumes
---

# Statically provision a NAS file system in the ACK console

This topic describes how to use a static NAS volume in the Container Service for Kubernetes \(ACK\) console.

-   An Apsara File Storage NAS \(NAS\) file system is created. For more information, see [Manage file systems]().
-   A mount target is created for the NAS file system. For more information, see [Manage mount targets]().

    **Note:**

    The mount target and the cluster node to which the NAS file system is mounted must be deployed in the same virtual private cloud \(VPC\).


## Create a PV

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**.

5.  Click the **Persistent Volumes** tab. On the **Persistent Volumes** tab, click **Create** in the upper-right corner.

6.  In the **Create PV** dialog box, configure the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PV Type**|You can select Cloud Disk, NAS, or OSS. In this example, select NAS.|
    |**Volume Name**|The name of the persistent volume \(PV\). The name must be unique in the namespace. In this example, enter pv-nas.|
    |**Volume Plug-in**|You can select Flexvolume or CSI. In this example, select CSI.|
    |**Capacity**|The capacity of the PV. The NAS file system provides unlimited capacity. This parameter does not limit the usage of the NAS file system but defines the capacity of the PV.|
    |**Access Mode**|You can select ReadWriteMany or ReadWriteOnce. Default value: ReadWriteMany.|
    |**Mount Target Domain Name**|The mount target of the NAS file system. You can select **Select Mount Target** or Custom.|
    |**Show Advanced Options**|    -   **Subdirectory**: the subdirectory in the NAS file system. The subdirectory must start with a forward slash \(/\). After you set this parameter, the PV is mounted to the subdirectory.
        -   If the specified subdirectory does not exist, the system automatically creates the subdirectory in the NAS file system.
        -   This parameter is optional. By default, the PV is mounted to the root directory of the NAS file system.
        -   To specify a subdirectory in a NAS Extreme file system, specify a subdirectory that starts with /share.
    -   **Version**: the version of the PV. |
    |**Label**|Attach labels to the PV.|

7.  Click **Create**.


## Create a PVC

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volume Claims**.

5.  On the **Persistent Volume Claims** tab, click **Create** in the upper-right corner.

6.  In the Create PVC dialog box, set the parameters.

    |Parameter|Description|
    |---------|-----------|
    |**PVC Type**|You can select Cloud Disk, NAS, or OSS. In this example, NAS is selected.|
    |**Name**|The name of the persistent volume claim \(PVC\). The name must be unique in the cluster.|
    |**Allocation Mode**|In this example, select Existing Volumes. **Note:** If no PV is created, you can set **Allocation Mode** to **Create Volume**. For more information, see [Create a PV](#section_vgf_lzl_djm). |
    |**Existing Volumes**|Click **Select PV**. Find the PV that you want to use and click **Select** in the Actions column.|
    |**Capacity**|The capacity of the PV. **Note:** The capacity claimed by the PVC cannot exceed the capacity of the PV to be bound to this PVC. |

7.  Click **Create**.

    After you create the PVC, you can find it on the Persistent Volume Claims tab. In this example, a PVC named csi-nas-pvc is created and a PV is bound to the PVC.


## Deploy an application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Deployments**.

5.  On the **Deployments** tab, click **Create from Image**.

6.  On the **Basic Information** wizard page, configure basic settings.

    |Parameter|Description|
    |---------|-----------|
    |Name|The name of the application.|
    |Replicas|The number of pods that are provisioned for the application. Default value: 2.|
    |Type|The type of application. You can select **Deployments**, **StatefulSets**, **Jobs**, ******Cron Jobs**, or **DaemonSets**.|
    |Label|Add a label to the application. Labels are used to identify the application.|
    |Annotations|Add an annotation to the application.|
    |Synchronize Timezone|Specify whether to synchronize the time zone between nodes and containers.|

    **Note:** **Deployments** is selected in this example.

7.  Click **Next**.

8.  Configure the container parameters.

    This example describes how to configure the volume parameters. For more information about other parameters, see [Use a Deployment to create a stateless application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Use a Deployment to create a stateless application.md).

    You can add local volumes and cloud volumes.

    -   **Local storage**: You can select HostPath, ConfigMap, Secret, or EmptyDir from the PV Type drop-down list. Then, specify Mount Source and Container Path to mount a source path to a container path. For more information, see [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/?spm=0.0.0.0.8VJbrE).
    -   **Cloud storage**: You can add cloud volumes.
    In this example, specify csi-nas-pvc as Mount Source and mount it to the /tmp path in the container.

    ![volume](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5616654061/p179580.png)

9.  Click **Next**.

10. Set other parameters and click **Create**.

    After you create the application, you can use the NAS volume in the application.


You can also use the statically provisioned NAS file system through a command-line interface \(CLI\). For more information, see [Mount a NAS file system as a static PV by using YAML files](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-CSI/NAS volumes/Mount a NAS file system as a static PV by using YAML files.md).


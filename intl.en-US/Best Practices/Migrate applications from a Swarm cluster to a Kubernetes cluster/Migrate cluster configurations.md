# Migrate cluster configurations

This topic uses a cluster of Container Service for Swarm named swarm-piggymetrics-cluster as an example to describe how to migrate cluster configurations from Swarm to Kubernetes. The following steps are involved: create a Kubernetes cluster, migrate node labels, check whether the Kubernetes cluster can access other Alibaba Cloud services, migrate volumes, and migrate configuration items.

The Swarm cluster to be migrated is attached with a Server Load Balancer \(SLB\) instance. For more information, see [Attach an SLB instance to a Swarm cluster](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Attach an SLB instance to a Swarm cluster.md).

This topic uses cluster swarm-piggymetrics-cluster as an example to describe how to migrate cluster configurations from Swarm to Kubernetes. The configurations migration process includes the following steps: create a Kubernetes cluster, migrate node labels, check whether the Kubernetes cluster can access other Alibaba Cloud services over a virtual private cloud \(VPC\), migrate volumes, and migrate configuration items.

## Create a Kubernetes cluster

Create a Kubernetes cluster based on the configurations of cluster swarm-piggymetrics-cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md). When you create the Kubernetes cluster, take note of the following limits:

-   Select the same region and zone where cluster swarm-piggymetrics-cluster is deployed. This ensures successful migration of resources that cannot be migrated across regions, such as virtual private clouds \(VPCs\)
-   Kubernetes clusters support only VPCs and do not support the classic network.
    -   If cluster swarm-piggymetrics-cluster is deployed in a VPC, you must select the same VPC when you create the managed Kubernetes cluster.
    -   If cluster swarm-piggymetrics-cluster is deployed in the classic network, you must migrate the cluster to a VPC. For more information, see [Hybrid migration](/intl.en-US/Best practices/Classic network-to-VPC migration/Overview.md).
-   When you create the managed Kubernetes cluster, you must select Install the CloudMonitor Agent on ECS Nodes. This allows you to view the monitoring information of the Elastic Compute Service \(ECS\) instances in the Cloud Monitor console after you switch traffic to the created Kubernetes cluster or perform canary releases in the created Kubernetes cluster.
-   If cluster swarm-piggymetrics-cluster uses Log Service, we recommend that you select **Enable Log Service** and the Log Service project that is used by the Swarm cluster. This way, you do not need to migrate Log Service configurations.
-   If cluster swarm-piggymetrics-cluster uses a Relational Database Service \(RDS\) instance, we recommend that you select the RDS instance to add the ECS instances of the managed Kubernetes cluster to the whitelist of the RDS instance. This ensures that the Kubernetes cluster can access the RDS instance.

## Migrate node labels

If nodes in cluster swarm-piggymetrics-cluster are added with custom labels that set rules for node deployment, you must migrate these custom labels. To migrate custom labels, perform the following steps:

1.  View the custom labels of nodes in the Swarm cluster.

    1.  Log on to the [Container Service for Swarm console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Clusters**. On the Clusters page, find cluster swarm-piggymetrics-cluster and click **Manage** in the Actions column.

    2.  In the left-side navigation pane, click **Custom Labels**. On the page that appears, you can find the custom labels that are added to the nodes in cluster swarm-piggymetrics-cluster.

2.  Add the same labels to the related nodes in the created managed Kubernetes cluster.

    1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com). In the left-side navigation pane, choose **ClusterS** \> **Nodes**. In the upper right corner of the Nodes page, click **Manage Labels**.

    2.  On the Manage Labels page, select a node and click **Add Label**. In the Add dialog box, enter the label name and value and click **OK**.


## Check whether the created Kubernetes cluster can access other Alibaba Cloud services over a VPC

If cluster swarm-piggymetrics-cluster is deployed in the classic network, the created managed Kubernetes cluster that is deployed in a VPC may fail to access other Alibaba Cloud services such as RDS and Object Storage Service \(OSS\). For example, only ECS instances in the RDS whitelist can access the related RDS instance. The endpoint of an OSS bucket for the classic network is different from that for a VPC. Therefore, after you complete the preceding steps, you must check whether the created Kubernetes cluster can access other Alibaba Cloud services over a VPC.

1.  Log on to the [RDS console](https://rdsnew.console.aliyun.com). On the Instances page, click the related RDS instance. In the left-side navigation pane of the details page, click **Data security**. In the **Whitelist Settings** section, check whether the IP addresses of the ECS instances in the created managed Kubernetes cluster are added to the whitelist of the RDS instance.

2.  In the left-side navigation pane, click **Database Connection**. On the **Instance Connection** tab, you can obtain the value of the Internal Endpoint parameter, which is the endpoint of the RDS instance for VPCs.

3.  Log on to the [ECS console](https://ecs.console.aliyun.com/). Then, follow the instructions to check whether the ECS instance can connect to the RDS instance over the VPC, as shown in the following figure.

    -   If the ECS instances can access the RDS instance, go to the next step.
    -   If the ECS instances cannot access the RDS instance, the possible reasons include:

        -   The IP addresses of the ECS instances failed to be added to the whitelist of the RDS instance. The IP addresses of the ECS instances in the created Kubernetes cluster failed to be added to the whitelist of the RDS instance. Log on to the [RDS console](https://rdsnew.console.aliyun.com), choose **RDS Instance** \> **Data Security**, and add the IP addresses of the ECS instances to the RDS whitelist again. For more information, see [Configure a whitelist for an ApsaraDB RDS for PostgreSQL instance](/intl.en-US/RDS PostgreSQL Database/Quick start/Configure a whitelist for an ApsaraDB RDS for PostgreSQL instance.md).
        -   If the issue still exists, see [What do I do if I cannot connect an ECS instance to an ApsaraDB for RDS instance?](/intl.en-US/FAQs/Connections and Networks/Resolve the issue that you cannot connect to an RDS instance.md).
        After you troubleshoot and fix the connection issue, log on to the [ECS console](https://ecs.console.aliyun.com/) again. Make sure that the ECS instances can access the RDS instance. Then, go to the next step.

4.  Log on to the [OSS console](https://oss.console.aliyun.com/). Find the OSS bucket that is used by the Swarm cluster and view the endpoints for the classic network and VPCs.


If cluster swarm-piggymetrics-cluster uses the endpoint for the classic network to access the OSS bucket, you must configure the Swarm cluster to access the OSS bucket by using the endpoint for VPCs. This endpoint setting is also required in the next step when you migrate volumes.

## Migrate volumes

In Swarm and Kubernetes, volumes are mounted to clusters. You can migrate volume configurations when you migrate cluster configurations. You can specify how to use the volumes when you migrate application configurations. Volumes in Swarm clusters correspond to persistent volumes \(PVs\) and persistent volume claims \(PVCs\) in Kubernetes clusters.

Swarm supports the following types of volumes: Network Attached Storage \(NAS\), cloud disk, and OSS. You can use them in Kubernetes **directly as volumes** or **through PVs and PVCs**. For more information, see [Storage management overview](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Overview.md). This section describes how to use volumes **through PVs or PVCs**.

1.  Log on to the [Container Service for Swarm console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Volumes**. On the Data Volume List page, select cluster swarm-piggymetrics-cluster and find volumes of the NAS, disk, and OSS types.

2.  In the created managed Kubernetes cluster, create PVs and PVCs for the three types of volumes that are mounted to the Swarm cluster. For more information, see the following topics:

    -   [Use Alibaba Cloud disks as volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/Disk volumes/Use Alibaba Cloud disks as volumes.md)
    -   [Use NAS volumes](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/NAS volumes/Use NAS volumes.md)
    -   [Use an OSS volume](/intl.en-US/User Guide for Kubernetes Clusters/Storage management-Flexvolume/OSS volumes/Use an OSS volume.md)

Notes on volume migration

-   A disk is a non-shared storage provided by Alibaba Cloud and can be mounted to only one pod at a time. If the Swarm and Kubernetes clusters write data to a disk at the same time, an error will occur. This means that you cannot migrate disks from Swarm to Kubernetes without service interruptions. We recommend that you use NAS or OSS for shared storage in Swarm clusters or migrate disks during off-peak hours. You must detach a disk from the Swarm cluster before you can mount it to the Kubernetes cluster. For more information, see [Detach a data disk](/intl.en-US/Block Storage/Cloud disks/Detach a data disk.md) and [View and delete data volumes](/intl.en-US/User Guide/Data volumes/View and delete data volumes.md).
-   In Kubernetes clusters, the name of a PV or a PVC cannot contain uppercase letters or underscores \(\_\). If the name of a volume in the Swarm cluster contains uppercase letters or underscores \(\_\), convert the name based on the following rules:
    -   Change uppercase letters to lowercase letters.
    -   Change underscores \(\_\) to hyphens \(-\).
-   When you create a PV of the OSS type, set Endpoint to VPC Endpoint because Kubernetes supports only VPCs.
-   When you create a PVC, convert the name of the related volume based on the preceding rules and use the result as the name of the PVC. When you use Kompose to convert a Swarm compose file to Kubernetes resource files, PVC names are generated in the same way.

## Migrate configuration items

In Swarm clusters, you can create a configuration file and set configuration items to manage environment variables for multiple containers in a unified manner. When you deploy an application, you can use the configuration file to replace variables that start with a dollar sign \($\) in the Swarm compose file with the actual values.

This is an advanced feature provided by the Container Service for Swarm console. ACK does not support this feature. The configuration files in the Swarm cluster are different from ConfigMaps in the Kubernetes cluster. This means that the configuration items in Swarm cannot be automatically migrated to Kubernetes. You must manually replace the variables in the Swarm compose file with the actual values. For more information, see [Overview](/intl.en-US/Best Practices/Migrate applications from a Swarm cluster to a Kubernetes cluster/Migrate application configurations/Overview.md).


# Overview

This topic describes how to migrate a self-managed Kubernetes cluster to Container Service for Kubernetes \(ACK\) without service disruption.

## Migration scheme

![Migration scheme](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6768989061/p209569.png)

## Procedure

1.  Create and configure an ACK cluster.

    O&M engineers create an ACK cluster and configure cluster resources. This step makes it easy for developers to migrate applications to Kubernetes. For more information, see [Create a dedicated Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).

    The following parameters are required when you create the ACK cluster:

    -   Cluster type: Select one of the following cluster types when you create the ACK cluster.
        -   Standard dedicated Kubernetes cluster.

            You need to create and manage the master and worker nodes of the ACK cluster. You have full control over the ACK cluster. You are charged for the resources that are used by the master and worker nodes.

        -   Standard managed Kubernetes cluster.

            You need to create and manage only the worker nodes of the ACK cluster. ACK creates and manages the master nodes. You are charged for only the resources that are used by the worker nodes.

    -   **Operating system**: Select an operating system based on your requirements.
        -   By default, the CentOS 7.6 or Aliyun Linux 2.1903 operating system is selected. We recommend that you keep the default settings.
        -   If the default system kernel cannot meet your requirements, you can use a custom image. For more information, see [Use a custom image to create an ACK cluster](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Use a custom image to create an ACK cluster.md).
    -   **VPC**: Select a virtual private cloud \(VPC\) and vSwitches for the ACK cluster.
    -   **SNAT**: Configure Source Network Address Translation \(SNAT\) entries for the VPC.
    -   **Public access**: Expose the API server of the ACK cluster with an elastic IP address \(EIP\).
    -   **Cloud Monitor agent**: Install the Cloud Monitor agent on the Elastic Compute Service \(ECS\) instances of the ACK cluster.
    -   **Log Service**: Install and configure the Log Service agent in the ACK cluster.
2.  Migrate data.
    -   Migrate databases.
        1.  Create a Relational Data Service \(RDS\) instance.
        2.  Configure an RDS whitelist to allow only specified IP addresses to access the RDS instance.
        3.  Configure a PrivateZone.

            Use the PrivateZone to resolve the domain name of the database to the IP address of the RDS instance. This saves you the need to modify the database configuration on applications.

        4.  Migrate the data in the MySQL database of the self-managed Kubernetes cluster.

            Use Data Transmission Service \(DTS\) to migrate the data from the MySQL database to the ApsaraDB for RDS database in full, incremental, or two-way synchronization mode. For more information, see [Migrate data from a user-created MySQL database to an ApsaraDB RDS for MySQL instance](/intl.en-US/Data Migration/Migration from a user-created database to Alibaba Cloud/Source database: MySQL/Migrate data from a user-created MySQL database to an ApsaraDB RDS for MySQL instance.md).

    -   Migrate data.
        1.  Activate Object Storage Service \(OSS\).
        2.  Create an OSS bucket.
        3.  Migrate the data that is stored in the self-managed Kubernetes cluster.

            Use the ossimport tool to migrate the data in batches from an on-premises server or a third-party cloud storage service to OSS. For example, you can choose a third-party cloud storage service such as Amazon S3, Microsoft Azure, or Tencent Cloud Object Storage. For more information, see [Architectures and configurations](/intl.en-US/Tools/ossimport/Architectures and configurations.md).

    -   Migrate images.
        1.  Create a Container Registry repository.
        2.  Set the credential that is used to access the created Container Registry repository.
        3.  Migrate the images of the self-managed Kubernetes cluster.

            You can use the image-syncer tool to migrate the images of the self-managed Kubernetes cluster to the Container Registry repository. For more information, see [Use image-syncer to migrate container images](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Migrate container images/Use image-syncer to migrate container images.md).

3.  Migrate application configurations.

    O&M engineers or developers use the Velero tool to migrate the cluster or application configurations. For more information, see [Migrate applications to an ACK cluster](/intl.en-US/Best Practices/Migrate a self-built Kubernetes cluster to Container Service for Kubernetes/Migrate applications to an ACK cluster.md).

    1.  Set up the migration environment.
        1.  Install the Velero client.
        2.  Create an OSS bucket.
        3.  Create a Resource Access Management \(RAM\) user and generate an AccessKey pair.
        4.  Deploy the Velero server.
    2.  Back up applications in the self-managed Kubernetes cluster.
        -   Back up applications without persistent volumes \(PVs\).
        -   Back up applications with PVs.
    3.  Restore applications in the ACK cluster.
        1.  Create a StorageClass.
        2.  Restore applications.
    4.  Update the application configurations.
        -   Update image registries.
        -   Optimize the method to expose the services.
        -   Modify the configurations to mount disks.
    5.  Debug and start the applications.
4.  Perform regression tests.

    Test engineers perform regression tests on the ACK cluster without interrupting the online business.

    1.  Configure the domain names for regression tests.
    2.  Test applications.
    3.  Check the logged application events.
    4.  Check the monitoring metrics of the applications.
5.  Switch all production traffic to ACK.

    O&M engineers modify the Domain Name System \(DNS\) configuration to switch traffic to the ACK cluster.

    1.  Use the DNS service: Modify the DNS configuration to switch the traffic.
    2.  Upgrade the configurations or code of the client to switch the traffic.
6.  Bring the self-managed Kubernetes cluster offline.

    O&M engineers check whether the ACK cluster can be accessed as expected. Then, O&M engineers bring the self-managed Kubernetes cluster offline.

    1.  Check whether the ACK cluster can receive and send traffic as expected.
    2.  Bring the self-managed Kubernetes cluster offline.
    3.  Clear the backup files stored in the OSS bucket that is created when you migrate applications.


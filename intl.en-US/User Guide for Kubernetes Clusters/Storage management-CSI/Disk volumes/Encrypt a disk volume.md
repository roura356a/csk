---
keyword: [KMS, encrypt a disk volume]
---

# Encrypt a disk volume

Data encryption is suitable for scenarios that require high security or have compliance requirements. You do not need to create or maintain the key management infrastructure. Encrypted storage helps you ensure data privacy and autonomy when data is stored on Elastic Compute Service \(ECS\) instances. This topic describes how to use the keys hosted in Key Management Service \(KMS\) to encrypt data stored in disk volumes.

-   A Container Service for Kubernetes \(ACK\) cluster is created, and the CSI plug-in is deployed in the cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   KMS is activated. For more information, see [Activate KMS](/intl.en-US/Quick Start/Activate KMS.md).
-   A kubectl client is connected to the cluster. For more information, see [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

## Features

Alibaba Cloud ECS uses the industry-standard AES-256 algorithm to encrypt disks. Keys contain service keys and customer-created keys. You can obtain customer-created keys by using the Bring Your Own Key \(BYOK\) feature. Encryption and decryption have minimal impacts on ECS instance performance.

-   Data in the operating system of an ECS instance is automatically encrypted when you create the ECS instance by using an encrypted system disk or image. The data is automatically decrypted when it is read. For more information about the procedure, see [Encrypt a system disk](/intl.en-US/Block Storage/Encrypt a disk/Encrypt a system disk.md).
-   The following types of data are automatically encrypted when you create an encrypted data disk and attach it to an ECS instance. The data is automatically decrypted when it is read. For more information about the procedure, see [Encrypt a data disk](/intl.en-US/Block Storage/Encrypt a disk/Encrypt a data disk.md).
    -   Static data stored on the encrypted disk.
    -   Data transmitted between the encrypted disk and the instance. Data in the operating system is not encrypted again.
    -   Data transmitted from the instance to a backend storage cluster.
    -   All snapshots created from the encrypted disk. These snapshots have the same encryption key as the disk.
    -   All disks created from the encrypted snapshots.

## Limits

-   You can encrypt data disks of the following categories: enhanced SSDs \(ESSDs\), standard SSDs, ultra disks, and basic disks.
-   You can encrypt system disks of the following categories: ESSDs, standard SSDs, and ultra disks.
-   You cannot encrypt local disks.
-   You can encrypt system disks only when you are copying their custom images. You cannot perform the following operations on encrypted system disks:
    -   Convert encrypted images to unencrypted images.
    -   Copy encrypted images across regions.
    -   Share encrypted images.
    -   Export encrypted images.
-   You cannot directly convert unencrypted disks to encrypted disks.
-   You cannot directly convert encrypted disks to unencrypted disks.

## Configure disk encryption

You can enable disk encryption only when you create a disk. When you mount or unmount a disk, no encryption related operations are involved.

1.  Create a StorageClass.

    1.  Create a file named sc-kms.yaml and copy the following code to the file:

        ```
        apiVersion: storage.k8s.io/v1
        kind: StorageClass
        metadata:
          name: csi-disk-encrypted
        provisioner: diskplugin.csi.alibabacloud.com
        parameters:
            fsType: ext4
            type: cloud_ssd
            encrypted: "true"
            kmsKeyId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        reclaimPolicy: Delete
        ```

        **Note:**

        -   `encrypted`: specifies whether to create an encrypted disk. `true` specifies that an encrypted disk is created.
        -   `kmsKeyId`: specifies the KMS key that is used when the disk is created. If you do not set this parameter, the default customer master key \(CMK\) is used. If the `encrypted` parameter is set to `false`, this parameter does not take effect.
    2.  Run the following command to create a StorageClass:

        ```
        kubectl create -f sc-kms.yaml
        ```

    3.  Run the following command to query the created StorageClass:

        ```
        kubectl get sc csi-disk
        ```

        Expected output:

        ```
        NAME       PROVISIONER                       AGE
        csi-disk   diskplugin.csi.alibabacloud.com   9m5s
        ```

2.  Create a persistent volume claim \(PVC\).

    1.  Create a file named sc-pvc.yaml and copy the following code to the file:

        ```
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: disk-pvc
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 20Gi
          storageClassName: csi-disk
        ```

    2.  Run the following command to create a PVC:

        ```
        kubectl create -f sc-pvc.yaml
        ```

    3.  Run the following command to query the created PVC:

        ```
        kubectl get pvc
        ```

        Expected output:

        ```
        NAME             STATUS   VOLUME                        CAPACITY   ACCESS MODES   STORAGECLASS   AGE
        disk-pvc         Bound    d-wz92s6d95go6ki9x****        25Gi       RWO            csi-disk       10m
        ```

    4.  Run the following command to query the persistent volume \(PV\) that is bound to the PVC:

        ```
        kubectl get pv
        ```

        Expected output:

        ```
        NAME                     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE
        d-wz92s6d95go6ki9x****   25Gi       RWO            Retain           Bound    default/disk-pvc     csi-disk                10m
        ```

        The preceding output shows that a disk is created and the disk ID is d-wz92s6d95go6ki9x\*\*\*\*.

3.  Check whether the disk is encrypted.

    1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Storage & Snapshots** \> **Disks**.

    3.  On the Disks page, verify that the d-wz92s6d95go6ki9x\*\*\*\* disk is **Encrypted**.



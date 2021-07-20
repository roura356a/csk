---
keyword: [KMS, encrypt an OSS volume]
---

# Encrypt an OSS volume

Data encryption is suitable for scenarios that require high security or have compliance requirements. You do not need to create or maintain the key management infrastructure. Encrypted storage helps you ensure data privacy and autonomy when data is stored on Elastic Compute Service \(ECS\) instances. This topic describes how to use the customer master keys \(CMKs\) hosted in Key Management Service \(KMS\) and the keys hosted in Object Storage Service \(OSS\) to encrypt data stored in OSS volumes.

-   A Container Service for Kubernetes \(ACK\) cluster is created and the CSI plug-in is deployed in the cluster. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   An OSS bucket is created. For more information, see [Create buckets](/intl.en-US/Quick Start/OSS console/Create buckets.md).

    **Note:** Select the internal endpoint if the OSS bucket and the ECS instance are deployed in the same region.

-   KMS is activated. For more information, see [Activate KMS](/intl.en-US/Quick Start/Activate KMS.md).
-   A kubectl client is connected to the cluster. For more information, see [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

## Encryption methods

OSS volumes support server-side encryption and client-side encryption. ACK supports only server-side encryption.

-   Server-side encryption encrypts data before the data is stored in the disk of an Alibaba Cloud data center, and automatically decrypts data when an object is downloaded.
-   Client-side encryption allows you to use an SDK to encrypt data on the client side before the data is uploaded to OSS.

OSS provides the following server-side encryption methods that you can use in different scenarios:

-   Use the default CMK or a specified CMK to encrypt OSS objects

    -   To use the default KMS-hosted CMK to encrypt OSS objects, set `HTTP X-OSS-server-side-encryption` to `KMS` and do not set the CMK ID when you upload object files.
    -   To use a specified KMS-hosted CMK to encrypt OSS objects, set `HTTP X-OSS-server-side-encryption` to `KMS` and set `X-OSS-server-side-encryption-key-id` to your `CMK ID` when you upload object files.
    **Note:** You are charged when you call API operations to encrypt or decrypt data with KMS keys. For more information about KMS billing, see [Billing](/intl.en-US/Pricing/Billing.md).

    This method is cost-effective because you do not need to send data to the KMS server over networks to encrypt and decrypt data.

-   Use an OSS-hosted key to encrypt OSS objects
    -   This encryption method is based on OSS and implemented as an attribute of objects.
    -   OSS generates and manages the keys that are used for data encryption based on the industry standard AES-256 strong encryption algorithm.
    -   You must set `HTTP X-OSS-server-side-encryption` to `AES256` when you upload object files.

Only one server-side encryption method can be used for an object at a time.

## Encrypt an OSS volume

You can use OSSFS to configure encryption parameters for OSS volumes. The parameters take effect when the OSS volume is mounted as a persistent volume \(PV\). For more information about OSSFS installation and setup, see [Install and set up OSSFS](/intl.en-US/Tools/ossfs/Overview.md).

OSSFS supports the following three encryption methods:

**Method 1: Use the default KMS-hosted CMK to encrypt OSS objects**

1.  Create a file named kms-cmk-default.yaml and copy the following code to the file:

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteOnce
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv
        volumeAttributes:
          bucket: "python"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
          akId: "LTAI4G6E3whAAKnzdRPx****"
          akSecret: "uRJeIi0cbahgWOhxncpp54wR5b****"
          path: "/"
          encrypted: "kms"
    ```

    |Parameter|Description|
    |---------|-----------|
    |akId|AccessKey ID|
    |akSecret|AccessKey Secret|
    |encrypted|Specifies the encryption method.    -   kms: specifies that the volume is encrypted by using KMS.
    -   aes256: specifies that the volume is encrypted by using the AES-256 algorithm. |

2.  Run the following command to create an encrypted OSS volume:

    ```
    kubectl create -f kms-cmk-default.yaml
    ```

3.  View the encrypted OSS volume in the ACK console.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. On the Persistent Volumes page, verify that the encrypted OSS volume that you newly created is displayed.


**Method 2: Use a specified KMS-hosted CMK to encrypt OSS objects**

1.  Create a file named kms-cmk.yaml and copy the following code to the file:

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteOnce
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv
        volumeAttributes:
          bucket: "python"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
          akId: "LTAI4G6E3whAAKnzdRPx****"
          akSecret: "uRJeIi0cbahgWOhxncpp54wR5b****"
          path: "/"
          encrypted: "kms"
          kmsKeyId: "1234abcd-12ab-34cd-56ef-12345678****"
    ```

2.  Run the following command to create an encrypted OSS volume:

    ```
    kubectl create -f kms-cmk.yaml
    ```

3.  View the encrypted OSS volume in the ACK console.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. On the Persistent Volumes page, verify that the encrypted OSS volume that you newly created is displayed.


**Use an OSS-hosted key to encrypt OSS objects**

1.  Create a file named sse-oss.yaml and copy the following code to the file:

    ```
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: oss-csi-pv
    spec:
      capacity:
        storage: 5Gi
      accessModes:
        - ReadWriteOnce
      csi:
        driver: ossplugin.csi.alibabacloud.com
        volumeHandle: oss-csi-pv
        volumeAttributes:
          bucket: "python"
          url: "oss-cn-hangzhou.aliyuncs.com"
          otherOpts: "-o max_stat_cache_size=0 -o allow_other"
          akId: "LTAI4G6E3whAAKnzdRPx****"
          akSecret: "uRJeIi0cbahgWOhxncpp54wR5b****"
          path: "/"
          encrypted: "aes256"
    ```

2.  Run the following command to create an encrypted OSS volume:

    ```
    kubectl create -f sse-oss.yaml
    ```

3.  View the encrypted OSS volume in the ACK console.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane of the ACK console, click **Clusters**.

    3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

    4.  In the left-side navigation pane of the details page, choose **Volumes** \> **Persistent Volumes**. On the Persistent Volumes page, verify that the encrypted OSS volume that you newly created is displayed.



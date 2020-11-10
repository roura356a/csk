# Use KMS to encrypt Kubernetes secrets at rest in the etcd

This topic describes how to use keys created in Key Management Service \(KMS\) to encrypt secrets in professional managed clusters of Alibaba Cloud Container Service for Kubernetes \(ACK\).

-   A customer master key \(CMK\) is created in the KMS console. For more information, see [Manage CMKs](/intl.en-US/Key service/Manage CMKs.md).

    **Note:** Professional managed ACK clusters support only CMKs of the Aliyun\_AES\_256 type and do not support automatic rotation of CMKs.

-   The ACK service account is authorized to assume the AliyunCSManagedSecurityRole role. If you use an unauthorized account to enable Kubernetes secret encryption at rest for a new or existing professional managed ACK cluster, you are prompted to authorize the account first.
-   If the current account is a RAM user, make sure it has the AliyunKMSCryptoAdminAccess permission. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

[Kubernetes secrets](https://kubernetes.io/docs/concepts/configuration/secret/) are used to store and manage sensitive data, such as passwords to applications, Transport Layer Security \(TLS\) certificates, and credentials to download Docker images. Kubernetes stores secrets in the etcd of the cluster.

You can use keys created in Key Management Service \(KMS\) to encrypt secrets in professional managed ACK clusters. Based on the envelope encryption mechanism, KMS automatically encrypts and decrypts secrets in the etcd of ACK clusters by using a [KMS provider](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/) provided by Kubernetes. For more information about envelope encryption, see [What is envelope encryption?](/intl.en-US/FAQ/What is envelope encryption?.md). The following list describes the process to encrypt and decrypt secrets in a professional managed ACK cluster:

-   When you use a Kubernetes secret to encrypt and store a password, the API server generates a random data encryption key \(DEK\) to encrypt the secret. Then, the API server returns the DEK to KMS. KMS uses the specified key to encrypt the DEK and returns the encrypted DEK to the API server. The API server then stores the encrypted secret and DEK in etcd.
-   When you decrypt the Kubernetes secret, the system calls the Decrypt API operation of KMS to decrypt the DEK. Then, the system uses the decrypted DEK to decrypt the Kubernetes secret and returns the password.

## Enable Kubernetes secret encryption at rest when you create a professional managed ACK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the page, click **Create Kubernetes Cluster**. On the Select Cluster Template page, select Professional Managed Cluster \(Preview\) and click **Create**.

4.  On the **ACK managed edition** tab, find the **Secret Encryption** section and select the **Select Key** check box. Then, select a CMK from the drop-down list. For more information about how to configure a professional managed ACK cluster, see [Create an ACK Pro cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create an ACK Pro cluster.md).

    ![Select Key](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1200105061/p180545.png)

    Log on to the [ActionTrail console](https://actiontrail.console.aliyun.com). In the left-side navigation pane, click **History Search**. On the History Search page, check for encryption or decryption operations that are performed by using the AliyunKMSCryptoAdminAccess role. If these operations exist, the secret encryption at rest feature is enabled.

    ![performer](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2700105061/p180546.png)


## Enable secret encryption at rest for an existing professional managed ACK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of the cluster for which you want to enable secret encryption.

4.  Click the **Basic Information** tab. In the **Basic Information** section, turn on the **Secret Encryption** switch.

    **Note:** If you are using a RAM user, make sure that the RAM user is granted the cluster administrator or O&M engineer role based on role-based access control \(RBAC\). For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

    If the status of the cluster changes from **Updating** to **Running**, the secret encryption at rest feature is enabled for the cluster.


## Disable secret encryption at rest for an existing professional managed ACK cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of the professional managed ACK cluster for which you want to disable secret encryption.

4.  Click the **Basic Information** tab. In the **Basic Information** section, turn off the **Secret Encryption** switch.

    **Note:** If you are using a RAM user, make sure that the RAM user is granted the cluster administrator or O&M engineer role based on role-based access control \(RBAC\). For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

    If the status of the cluster changes from **Updating** to **Running**, the secret encryption at rest feature is disabled for the cluster.



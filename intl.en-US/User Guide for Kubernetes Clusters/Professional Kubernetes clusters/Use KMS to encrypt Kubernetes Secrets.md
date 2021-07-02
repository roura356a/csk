# Use KMS to encrypt Kubernetes Secrets

In professional managed Kubernetes clusters, you can use keys that are created in Key Management Service \(KMS\) to encrypt Kubernetes Secrets. This topic describes how to use a key that is managed by KMS to encrypt Secrets for a professional managed Kubernetes cluster.

-   A customer master key \(CMK\) is created in the KMS console. For more information, see [Manage CMKs]().

    **Note:** Professional managed ACK clusters support only CMKs of the Aliyun\_AES\_256 type and do not support automatic rotation of CMKs.

-   The current account is authorized to assume the AliyunCSManagedSecurityRole role. If you use an unauthorized account to enable Kubernetes Secret encryption for a new or existing professional managed ACK cluster, you are prompted to authorize the account first.
-   The Resource Access Management \(RAM\) user is granted the AliyunKMSCryptoAdminAccess permission if your account is a RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Authorization management/Grant permissions to a RAM user.md).
-   KMS charges a fee for every 10 thousand API calls. KMS also charges for the keys that you have uploaded to KMS. After Secret encryption is enabled for the professional Kubernetes cluster, kube-apiserver must call the encryption and decryption API operations of KMS to perform read and write operations on Secrets. Make sure that your account balance is sufficient. If your account has been overdue for seven days, you cannot manage the cluster. For more information about how KMS is billed, see [Billing](/intl.en-US/Pricing/Billing.md).

In Kubernetes clusters, [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/) are used to store and manage sensitive data, such as passwords to applications, Transport Layer Security \(TLS\) certificates, and credentials to download Docker images. Kubernetes stores Secrets in the etcd of a cluster.

You can use keys created in KMS to encrypt Secrets for professional managed Kubernetes clusters. KMS uses envelop encryption to encrypt and decrypt Secrets that are stored in etcd based on the [KMS Encryption Provider](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/) mechanism. For more information about envelop encryption, see [What is envelope encryption?](/intl.en-US/FAQ/What is envelope encryption?.md). The following content explains how to encrypt and decrypt Kubernetes Secrets:

-   When you use a Kubernetes Secret to encrypt and store a password, the API server generates a random data encryption key \(DEK\) to encrypt the Secret. Then, the API server sends the DEK to KMS. KMS uses the specified key to encrypt the DEK and returns the encrypted DEK to the API server. The API server then stores the encrypted Secret and DEK in etcd.
-   When you decrypt the Kubernetes Secret, the system calls the Decrypt API operation of KMS to decrypt the DEK. Then, the system uses the decrypted DEK to decrypt the Kubernetes Secret and returns the password.

## Enable Secret encryption when you create a professional managed Kubernetes cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  In the upper-right corner of the page, click **Create Kubernetes Cluster**. On the Select Cluster Template page, select Professional Managed Kubernetes Cluster and click **Create**.

4.  On the **Managed Kubernetes** tab, find **Secret Encryption**, select **Select Key**, and then select a key from the drop-down list. For more information about how to configure a professional managed Kubernetes cluster, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

    ![Cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4354669161/p133261.png)

    Log on to the [ActionTrail console](https://actiontrail.console.aliyun.com). In the left-side navigation pane, click **Event Detail Query**. On the Event Detail Query page, check for encryption and decryption operations that are performed by assuming the aliyuncsmanagedsecurityrole role. If these operations are performed, the Secret encryption feature is enabled.

    ![Verify the result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4354669161/p133314.png)


## Enable Secret encryption for an existing professional managed Kubernetes cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, click the name of the professional managed Kubernetes cluster for which you want to enable Secret encryption.

4.  On the details page of the cluster, click the **Basic Information** tab. In the **Basic Information** section, turn on the **Secret Encryption** switch.

    **Note:** If you log on to the ACK console as a RAM user, make sure that the RAM user is assigned the administrator or O&M engineer role based on role-based access control \(RBAC\). For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

    If the status of the cluster changes from **Updating** to **Running**, the Secret encryption feature is enabled for the cluster.


## Disable Secret encryption for an existing professional managed Kubernetes cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, click the name of the professional managed Kubernetes cluster for which you want to disable Secret encryption.

4.  On the details page of the cluster, click the **Basic Information** tab. In the **Basic Information** section, turn off the **Secret Encryption** switch.

    **Note:** If you log on to the ACK console as a RAM user, make sure that the RAM user is assigned the administrator or O&M engineer role based on RBAC. For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

    If the status of the cluster changes from **Updating** to **Running**, the Secret encryption feature is disabled for the cluster.



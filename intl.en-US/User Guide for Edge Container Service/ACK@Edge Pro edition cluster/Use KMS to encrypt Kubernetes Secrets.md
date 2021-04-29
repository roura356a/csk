---
keyword: [encrypt Secrets, professional edge Kubernetes cluster, KMS]
---

# Use KMS to encrypt Kubernetes Secrets

In a professional edge Kubernetes cluster, you can use a key that is created by using Key Management Service \(KMS\) to encrypt Kubernetes Secrets. This topic describes how to use a key that is managed by KMS to encrypt Secrets for a professional edge Kubernetes cluster.

-   A customer master key \(CMK\) is created in the KMS console. For more information, see [Manage CMKs]().

    **Note:** Professional edge Kubernetes clusters support only CMKs of the Aliyun\_AES\_256 type and do not support automatic rotation of CMKs.

-   The current account is authorized to assume the AliyunCSManagedSecurityRole role. If you use an unauthorized account to enable Kubernetes Secret encryption for a new or existing professional edge Kubernetes cluster, you are prompted to authorize the account first.
-   The Resource Access Management \(RAM\) user is granted the AliyunKMSCryptoAdminAccess permission if your account is a RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

Kubernetes Secrets are used to store and manage sensitive data, such as passwords to applications, Transport Layer Security \(TLS\) certificates, and credentials to download Docker images. Kubernetes stores Secrets in the etcd of the cluster. For more information about Kubernetes Secretes, see [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/).

You can use keys created in KMS to encrypt Secrets for professional edge Kubernetes clusters. KMS uses envelop encryption to encrypt and decrypt Secrets that are stored in etcd based on the [KMS encryption provider](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/). For more information about envelope encryption, see [What is envelop encryption?](/intl.en-US/FAQ/What is envelope encryption?.md) The following content explains how to encrypt and decrypt Kubernetes Secrets:

-   When you use a Kubernetes Secret to encrypt and store a password, the API server generates a random data encryption key \(DEK\) to encrypt the Secret. Then, the API server sends the DEK to KMS. KMS uses the specified key to encrypt the DEK and returns the encrypted DEK to the API server. The API server then stores the encrypted Secret and DEK in etcd.
-   When you decrypt the Kubernetes Secret, the system calls the Decrypt API operation of KMS to decrypt the DEK. Then, the system uses the decrypted DEK to decrypt the Kubernetes Secret and returns the password.

## Enable Secrete encryption when you create a professional edge Kubernetes cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  In the upper-right corner of the Clusters page, click **Create Kubernetes Cluster**.

4.  Click the **Managed Edge Kubernetes** tab.

5.  On the **Managed Edge Kubernetes** tab, find **Secret Encryption**, select the **Select Key** check box, and then select a key from the drop-down list. For more information about how to configure a professional edge Kubernetes cluster, see [Create a professional edge Kubernetes cluster \(private preview\)]().

    ![Cluster](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4354669161/p133261.png)

    Log on to the [ActionTrail console](https://actiontrail.console.aliyun.com). In the left-side navigation pane, click **Event Detail Query**. On the Event Detail Query page, check for encryption and decryption operations that are performed by assuming the aliyuncsmanagedsecurityrole role. If these operations exist, the Secret encryption feature is enabled.

    ![Verify the result](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4354669161/p133314.png)


## Enable Secret encryption for an existing professional edge Kubernetes cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of the professional edge Kubernetes cluster for which you want to enable Secret encryption.

4.  On the details page of the cluster, click the **Basic Information** tab. In the **Basic Information** section, turn on the **Secret Encryption** switch.

    **Note:** If you log on as a RAM user, make sure that the RAM user is assigned the administrator or O&M engineer role based on role-based access control \(RBAC\). For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

    If the status of the cluster changes from **Updating** to **Running**, the Secret encryption is enabled for the cluster.


## Disable Secret encryption for an existing professional edge Kubernetes cluster

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of the professional edge Kubernetes cluster for which you want to disable Secret encryption.

4.  On the details page of the cluster, click the **Basic Information** tab. In the **Basic Information** section, turn off the **Secret Encryption** switch.

    **Note:** If you log on as a RAM user, make sure that the RAM user is assigned the administrator or O&M engineer role based on role-based access control \(RBAC\). For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).

    If the status of the cluster changes from **Updating** to **Running**, the Secret encryption feature is disabled for the cluster.



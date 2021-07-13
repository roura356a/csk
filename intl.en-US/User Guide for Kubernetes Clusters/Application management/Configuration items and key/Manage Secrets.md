---
keyword: [Secrets, Secret]
---

# Manage Secrets

This topic describes how to manage Secrets in the Container Service for Kubernetes \(ACK\) console.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

We recommend that you use Secrets to store sensitive information in Kubernetes clusters. The information includes passwords and certificates.

Secrets are classified into the following types:

-   Service account: A service account is automatically created by Kubernetes and automatically mounted to the /run/secrets/kubernetes.io/serviceaccount directory of a pod. The service account provides an identity for the pod to interact with the API server.
-   Opaque: This type of Secret is encoded in Base64 and used to store sensitive information, such as passwords and certificates.

By default, you can create only Opaque Secrets in the ACK console. Opaque Secrets store map type data. Therefore, values must be encoded in Base64. You can create Secrets in the ACK console with a few clicks. Plaintext is automatically encoded in Base64.

You can also create Secrets by using the CLI. For more information, see [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/).

## Create a Secret

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **Secrets**.

5.  On the Secrets page, click **Create** in the upper-right corner.

6.  In the **Create** panel, configure the Secret.

    Configure the parameters described in the following table.

    |Parameter|Description|
    |---------|-----------|
    |Name|Enter a name for the Secret. The name must be 1 to 253 characters in length, and can contain only lowercase letters, digits, hyphens \(-\), and periods \(.\).|
    |Namespace|Specify the namespace of the Secret.|
    |Type|You can select Opaque, Private Repository Logon Secret, and TLS Certificate.|
    |Opaque|If you set Type to Opaque, configure the following parameters:    -   To enter Secret data in plaintext, select **Encode Data Values Using Base64**.
    -   Configure the Secret in key-value pairs. Click **+ Add**. Enter the keys and values for the Secret in the **Name** and **Value** fields. |
    |Private Repository Logon Secret|If you set Type to Private Repository Logon Secret, configure the following parameters:    -   Docker Registry URL: Enter the address of the Docker registry where the Secret is stored.
    -   Username: Enter the username that is used to log on to the Docker registry.
    -   Password: Enter the password that is used to log on to the Docker registry. |
    |TLS Certificate|If you set Type to TLS Certificate, configure the following parameters:    -   Cert: Enter a TLS certificate.
    -   Key: Enter the key of the TLS certificate. |


## View a Secret

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **Secrets**.

5.  On the Secrets page, find the Secret that you want to view and click **Details** in the **Actions** column.

    On the details page of the Secret, you can view details about the Secret. You can also view the key-value pairs in the Secret.

    **Note:** To view the values in plaintext, click the icon in the **Value** column.


## Modify a Secret

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **Secrets**.

5.  On the Secrets page, find the Secret that you want to modify and click **Edit** in the **Actions** column.

6.  In the Edit panel, modify the Secret based on your requirements and click **OK**.


## Delete a Secret

**Note:** Do not delete Secrets that are created together with the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **Secrets**.

5.  On the Secrets page, find the Secret that you want to delete and click **Delete** in the Actions column. In the Confirm message that appears, click **OK**.



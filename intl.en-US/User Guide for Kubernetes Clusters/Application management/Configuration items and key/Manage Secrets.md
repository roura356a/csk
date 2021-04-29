# Manage Secrets

This topic describes how to manage Secrets in the Container Service for Kubernetes \(ACK\) console.

An ACK cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

We recommend that you use Secrets to store sensitive information in Kubernetes clusters, such as passwords and certificates.

Secrets are classified into the following types:

-   Service account: A service account is automatically created by Kubernetes and automatically mounted to the /run/secrets/kubernetes.io/serviceaccount directory of a pod. The service account provides an identity for the pod to interact with the API server.
-   Opaque: This type of Secret is encoded in Base64 and used to store sensitive information, such as passwords and certificates.

By default, you can create only Opaque Secrets in the ACK console. Opaque Secrets store map type data. Therefore, values must be encoded in Base64. You can create Secrets in the ACK console with a few clicks. Plaintext is automatically encoded in Base64.

You can also create Secrets by using the command-line interface \(CLI\). For more information, see [Kubernetes Secrets](https://kubernetes.io/docs/concepts/configuration/secret/).

## Create a Secret

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **Secrets** page, click **Create** in the upper-right corner of the page.

6.  In the **Create** panel, configure the Secret.

    1.  **Name**: Enter a name for the Secret. The name must be 1 to 253 characters in length, and can contain only lowercase letters, digits, hyphens \(-\), and periods \(.\).

    2.  **Type**: Select a Secret type.

    3.  To enter Secret data in plaintext, select **Encode Data Values Using Base64**.

    4.  Configure the Secret in key-value pairs. Click **+ Add**. Enter the keys and values for the Secret in the **Name** and **Value** fields.

    5.  Click **OK**.


## View a Secret

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **Secrets** page, find the Secret that you want to view and click its name.

    On the details page of the Secret, you can view details about the Secret. You can also view the key-value pairs in the Secret.

    **Note:** To view the values in plaintext, click the icon in the **Value** column.


## Modify a Secret

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **Secrets** page, find the Secret that you want to modify and click **Edit** in the **Actions** column.

6.  In the Edit panel, modify keys and values based on your requirements and click **OK**.


## Delete a Secret

**Note:** Do not delete Secrets that are created together with the cluster.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

5.  On the **Secrets** page, find the Secret that you want to delete and click **Delete** in the Actions column. In the message that appears, click **OK**.



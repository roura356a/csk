# Create a Secret

This topic describes how to create a Secret in a cluster of Container Service for Kubernetes \(ACK\) in the ACK console.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).

We recommend that you use Secrets to store sensitive information in Kubernetes clusters, such as passwords and certificates.

Secrets are classified into the following types:

-   Service Account: This type of secret is automatically created by Kubernetes and is automatically mounted to the /run/secrets/kubernetes.io/serviceaccount directory of a pod. You can use this type of Secret to access the Kubernetes API server.
-   Opaque: This type of secret is encoded in Base64 and used to store sensitive information, such as passwords and certificates.

You can create only Opaque Secrets in the ACK console. Opaque Secrets store map type data. Therefore, values must be encoded in Base64. You can quickly create a Secret in the ACK console. The plaintext is automatically encoded in Base64.

You can also create Secrets by using the command-line interface \(CLI\). For more information, see [Kubernetes secrets](https://kubernetes.io/docs/concepts/configuration/secret/).

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click **Details** in the **Actions** column.

4.  In the left-side navigation pane, click **Configurations**.

5.  Click the **Secrets** tab. In the upper-right corner of the **Secrets** tab, click **Create**.

6.  In the **Create** pane, configure the Secret.

    1.  **Name**: Enter a name for the Secret. The name must be 1 to 253 characters in length, and can contain only lowercase letters, digits, hyphens \(-\), and periods \(.\).

    2.  **Type**: Select a Secret type.

    3.  To enter Secret data in plaintext, select **Encode Data Values Using Base64**.

    4.  Enter key-value pairs. Click **+ Add** to enter **keys** and **values**.

    5.  Click **OK**.



---
keyword: [create a namespace, modify a namespace, delete a namespace]
---

# Manage namespaces

You can create namespaces as workspaces to complete different tasks. This topic describes how to create, modify, and delete namespaces in a Kubernetes cluster.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

In a Kubernetes cluster, you can use namespaces to create multiple virtual clusters. If multiple users share a cluster, you can create namespaces as workspaces to complete different tasks and use [resource quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/) to allocate resources in the namespaces.

## Create a namespace

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Namespaces and Quotas**.

5.  On the page that appears, click **Create** in the upper-right corner.

6.  In the **Create Namespace** dialog box, set the parameters. Then, click **OK**.

    -   **Name**: Enter a name for the namespace. In this example, the name is set to test. The name must be 1 to 63 characters in length and can contain only digits, letters, and hyphens \(-\). The name must start and end with a letter or digit.
    -   **Label**: Add one or more labels to the namespace. Labels are used to identify namespaces. For example, you can label a namespace as one used in the test environment.

        To add a label, enter a key and a value and click **Add** in the Actions column.

    Return to the Namespace page. In the list of namespaces, you can view that the namespace named test is created.


## Edit a namespace

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Namespaces and Quotas**.

5.  On the Namespace page, find the namespace that you want to modify and click **Edit** in the **Actions** column.

6.  In the dialog box that appears, click **Edit** to modify the label of the namespace. For example, modify the key-value pair of the label to `env:test-V2`. Then, click **OK**.


## Delete a namespace

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Namespaces and Quotas**.

5.  Find the namespace that you want to delete and click **Delete** in the **Actions** column.

6.  In the **Note** message, click **Confirm**.

    Return to the Namespace page. You can find that the namespace is deleted. Resource objects in the namespace are also deleted.



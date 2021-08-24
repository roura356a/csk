# Revoke a KubeConfig credential

In common multi-tenant scenarios, Alibaba Cloud Container Service for Kubernetes \(ACK\) issues KubeConfig credentials to users of different roles. These KubeConfig credentials contain unique identity information about the users and are used to connect to ACK clusters. If an employee leaves the company, you can revoke the KubeConfig credentials assigned to the employee. If a KubeConfig credential is suspected to be leaked, you can revoke the KubeConfig credential to ensure cluster security. This topic describes how to revoke a KubeConfig credential in the ACK console.

-   If you want to revoke a KubeConfig credential that is used to access a serverless Kubernetes cluster, select a cluster that was created after September 6, 2019.
-   If you want to revoke a KubeConfig credential that is used to access a dedicated or managed Kubernetes cluster, pay attention to the following notes:
    -   If you are using an Alibaba Cloud account, you can revoke your own KubeConfig credentials that are used to access clusters that were created no earlier than October 15, 2019.
    -   If you are a RAM user, you can revoke KubeConfig credentials that are used to access clusters that you can manage.

KubeConfig credentials can be revoked in the following two scenarios:

-   Revoke your own KubeConfig credentials.
-   Log on to your Alibaba Cloud account to revoke KubeConfig credentials that are issued to RAM users.

## Revoke your own KubeConfig credentials

**Note:** After a KubeConfig credential is revoked, you cannot use the credential to connect to the cluster. Perform this operation with caution.

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to view and click **Details** in the **Actions** column.

    **Note:**

    If you are using an Alibaba Cloud account, select a cluster that was created no earlier than October 15, 2019.

    If you want to revoke a KubeConfig credential that is used to access a serverless Kubernetes cluster, select a cluster that was created after September 6, 2019.

4.  On the **Cluster Information** page, click the **Connection Information** tab, and then click **Revoke KubeConfig**.

    ![Kubeconfig credential](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5741979261/p66613.png)

5.  In the dialog box that appears, click **OK**.

    Your KubeConfig credential that is used to access the selected cluster is revoked. The system then automatically assigns you a new KubeConfig credential.


## Use an Alibaba Cloud account to revoke a KubeConfig credential from a RAM user

1.  Log on to the [Container Service for Kubernetes console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Authorizations**.

3.  On the **RAM Users** tab of the Authorizations page, find the RAM user from which you want to revoke the KubeConfig credential and click **Revoke KubeConfig** on the right.

    In the dialog box that appears, you can find the IDs of all clusters that the selected RAM user can access.

    ![Revoke the credential of a RAM user](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/5255359951/p66623.png)

4.  Find the cluster for which you want to revoke the KubeConfig credential and click **Revoke KubeConfig**.

5.  In the dialog box that appears, click **OK**.



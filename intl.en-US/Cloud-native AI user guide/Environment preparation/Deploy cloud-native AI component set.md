---
keyword: [install cloud-native AI component set, configure AI Dashboard]
---

# Deploy cloud-native AI component set

You can install cloud-native AI component set only in professional Kubernetes clusters. This topic describes how to install cloud-native AI component set and how to configure AI Dashboard.

-   A professional Kubernetes cluster is created. Make sure that **Monitoring Agents** and **Log Service** are enabled on the Component Configurations wizard page when you create the cluster. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The Kubernetes version of the professional managed Kubernetes cluster is V1.18 or later.

## Deploy cloud-native AI component set

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **AI Project Acceleration \(Public Preview\)**.

5.  On the **Cloud-native AI Component Set** page, click **Deploy**.

6.  On the Cloud-native AI Component Set page, select the components that you want to deploy and click **Deploy Cloud-native AI Component Set**. The system checks the environment and dependencies, and then automatically deploys the components after the check is passed.

    **Note:**

    -   **Arena CLI \(Required\)** in the **Interaction Mode** section is required and automatically selected.
    -   Select **AI-Dashboard Console**. The **Note** dialog box appears. For more information, see Step [1](#step_twc_y20_m4t).
    After the components are installed, you can view the following information in the Components list:

    -   You can view the names and versions of the components that are installed in the cluster. You can also **deploy** components that are not installed or **uninstall** deployed components.
    -   If a component is upgradable, you can also **upgrade** the component.
    -   After the AI Dashboard component is installed, you can find the address of AI Dashboard in the upper-left corner of the page. You can access the AI Dashboard console through the address.

## Install and configure the AI Dashboard console

1.  If you select **AI Dashboard Console** on the Cloud-native AI Component Set page, the **Note** dialog box appears, as shown in the following figure.

    ![K-AI-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5101579161/p237448.png)

    **Note:**

    -   If you use **public domain names** to provide access to the AI Dashboard console, we recommend that you implement access control.
    -   If you want to enable **internal access** to the AI Dashboard console, select **Private IP** in the **Note** dialog box.
2.  In the **Note** dialog box, click the link in Step 1 to grant the cluster the required permissions.

    1.  On the **RAM Roles** page, click the **Permissions** tab and click the policy that you want to use.

    2.  Click **Modify Policy Document** on the **Policy Document** tab. In the **Modify Policy Document** panel, add the following content to the `Action` field in the **Policy Document** section:

        ```
        "ecs:DescribeInstances",
        "ecs:DescribeSpotPriceHistory",
        "ecs:DescribePrice",
        "eci:DescribeContainerGroups",
        "eci:DescribeContainerGroupPrice",
        "log:GetLogStoreLogs",
        "ims:CreateApplication",
        "ims:GetApplication",
        "ims:ListApplications",
        "ims:DeleteApplication",
        "ims:CreateAppSecret",
        "ims:GetAppSecret",
        "ims:ListAppSecretIds"
        ```

    3.  Click **OK**.

3.  On the **Cloud-native AI Component Set** page, select **Monitoring Component**.

    ![K-AI-3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5101579161/p237487.png)

4.  Click **Deploy Cloud-native AI Component Set**.



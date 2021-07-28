---
keyword: [cloud-native AI component set, AI Dashboard, AI Developer Console]
---

# Deploy the cloud-native AI component set

You can deploy the cloud-native AI component set only in professional Kubernetes clusters. This topic describes how to deploy the cloud-native AI component set in professional Kubernetes clusters, and how to install and configure AI Dashboard and AI Developer Console.

-   A professional Kubernetes cluster is created. Make sure that **Monitoring Agents** and **Log Service** are enabled on the Component Configurations wizard page when you create the cluster. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).

    **Note:** The preceding prerequisites are for installing and configuring only AI Dashboard.

-   The Kubernetes version of the professional Kubernetes cluster is 1.18 or later.

## Deploy the cloud-native AI component set

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **AI Project Acceleration \(Public Preview\)**.

5.  On the **Cloud-native AI Component Set** page, click **Deploy**.

6.  On the Cloud-native AI Component Set page, select the components that you want to deploy and click **Deploy Cloud-native AI Component Set**. The system checks the environment and dependencies, and then automatically deploys the components after the check is passed.

    **Note:**

    -   **Arena \(Required\)** in the **Interaction Mode** section is automatically selected.
    -   After you select **O&M Console**, the **Note** dialog box appears. For more information, see [Install and configure AI Dashboard](#section_93j_4nk_ojr).
    -   After you select **Development Console**, another **Note** dialog box appears. For more information, see [Install and configure AI Developer Console](#section_oj7_fkl_2tc).
    After the components are installed, you can view the following information in the Components list:

    -   You can view the names and versions of the components that are installed in the cluster. You can also **deploy** components that are not installed or **uninstall** deployed components.
    -   If a component is upgradable, you can also **upgrade** the component.
    -   After you install **ack-ai-dashboard** and **ack-ai-dev-console**, you can find the hyperlinks of **AI Dashboard** and **AI Developer Console** in the upper-left corner of the **Cloud-native AI Component Set** page. You can click a hyperlink to log on to the corresponding console.

## Install and configure AI Dashboard

1.  In the **Interaction Mode** section of the Cloud-native AI Component Set page, select **O&M Console**. Then, a **Note** dialog box appears, as shown in the following figure.

    ![K-AI-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6790447261/p237448.png)

    **Note:**

    -   If you select **Public Domain** and use a public domain name to access AI Dashboard, you must configure access control policies.
    -   If you want to use an **internal endpoint** to access AI Dashboard, select **Private IP** in the **Note** dialog box.
2.  In the **Note** dialog box, click the hyperlink to grant the cluster the required permissions.

    1.  On the **RAM Roles** page, click the **Permissions** tab and click the policy that you want to manage.

    2.  Click **Modify Policy Document** on the **Policy Document** tab. In the **Modify Policy Document** panel, add the following content to the `Action` field in the **Policy Document** section:

        ```
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
        "ims:ListAppSecretIds",
        "ims:ListUsers"
        ```

    3.  Click **OK**.

3.  On the **Cloud-native AI Component Set** page, select **Monitoring Component**.

    ![K-AI-3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5101579161/p237487.png)

4.  Click **Deploy Cloud-native AI Component Set**.


## Install and configure AI Developer Console

-   If no component in the cloud-native AI component set is installed, you can click **Deploy** on the **Cloud-native AI Component Set** page.
    1.  In the **Interaction Mode** section, select **Development Console**. Then, a **Note** dialog box appears, as shown in the following figure. Follow the instructions in the **Note** dialog box to grant the cluster the required permissions and select a method to access AI Developer Console. For more information about how to grant the cluster the required permissions, see Step 2 in the Install and configure AI Dashboard section.

        **Note:** **Arena \(Required\)** in the **Interaction Mode** section is automatically selected.

        ![A33](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4349737261/p275061.png)

    2.  Click **Deploy Cloud-native AI Component Set**.
-   If you have installed some components in the cloud-native AI component set, find **ack-ai-dev-console** in the components list of the **Cloud-native AI Component Set** page. Then, click **Deploy** in the **Actions** column to install AI Developer Console.


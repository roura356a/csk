---
keyword: [install Cloud-native AI Component Set, configure access to AI Dashboard, configure RAM]
---

# Install Cloud-native AI Component Set and access AI Dashboard

You can access AI Dashboard by using sshuttle, an SSL VPN, or an Ingress. You can install Cloud-native AI Component Set only in professional Kubernetes clusters. This topic describes how to install Cloud-native AI Component Set in a professional Kubernetes cluster and how to configure access to AI Dashboard by using three methods.

-   A professional Kubernetes cluster is created. For more information, see [Create a professional managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Professional Kubernetes clusters/Create a professional managed Kubernetes cluster.md).
-   The Kubernetes version is 1.18 or later for a professional managed Kubernetes cluster.
-   The following conditions are met before you configure access to AI Dashboard by using sshuttle:
    -   A jump server for which SSH access is enabled is created in the cluster.
    -   Python 3.6 or later is installed on the jump server.
    -   The username and password that you can use to connect to the jump server are obtained.
    -   sshuttle is the most cost-effective solution to set up a VPN over SSH. We recommend that you enable an IP whitelist for the security group to which the cluster belongs. The IP whitelist specifies the IP addresses that the cluster is allowed to access over the Internet. For more information, see [Allow your instance to connect to only specified public IP addresses](https://help.aliyun.com/document_detail/25475.html?spm=5176.2020520101securityGroupDetail.0.dexternal.2d004df5d6w9mV#specifyIpAccess).

## Deploy Cloud-native AI Component Set

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Applications** \> **AI Project Acceleration \(Public Preview\)**.

5.  On the **Cloud Native AI Component Set** page, click **Deploy**.

6.  On the Deploy Cloud Native AI Component Set page, select the components that you want to deploy and click **Deploy Cloud Native AI Component Set**. The system checks the environment and dependencies, and then automatically deploys the components after the check is passed.

    **Note:**

    -   **Arena CLI \(Required\)** in the **Interaction Mode** section is required.
    -   If **AI-Dashboard Console** is selected, a dialog box appears, which prompts you to grant the cluster the required permissions and configure an administrator. For more information, see Step [1](#step_twc_y20_m4t).
    After the components are installed, you can view the following information on the Add-ons page:

    -   You can view the components that are installed in the current cluster and the version information about the components. You can click **Deploy** or **Uninstall** to deploy or uninstall the components.
    -   If new versions are available for installed components, you can click **Upgrade** to upgrade the components.
    -   After the AI Dashboard component is installed, you can find the access address of AI Dashboard in the upper-left corner of the page. You can click the address to go to the AI Dashboard console.

## Install and configure AI Dashboard

**Step 1: Configure RAM**

1.  [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).

2.  [Create an application]().

3.  [Add OAuth scopes]().

4.  [Create an application secret]().


**Step 2: Install AI Dashboard**

1.  If you select **AI-Dashboard Console** on the Deploy Cloud Native AI Component Set page, the following dialog box appears, which prompts you to grant the cluster the required permissions and configure an administrator.

    ![K-AI-2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5101579161/p237448.png)

2.  In the dialog box, click the link in Step 1 to grant the cluster the required permissions.

    1.  On the **RAM Roles** page, click the **Permissions** tab and then click the target permission policy.

    2.  Click **Modify Policy Document**. In the **Modify Policy Document** panel, add the following content to the `Action` field:

        ```
        "ecs:DescribeInstances",
        "ecs:DescribeSpotPriceHistory",
        "ecs:DescribePrice",
        "eci:DescribeContainerGroups",
        "eci:DescribeContainerGroupPrice",
        "log:GetLogStoreLogs"
        ```

    3.  Click **OK**.

3.  Specify the administrator ID. Specify the ID of your Alibaba Cloud account, the AccessKey ID, and AccessKey secret that you obtained in [Configure RAM](#section_93j_4nk_ojr).

4.  On the **Deploy Cloud Native AI Component Set** page, select **Monitoring Component**.

    ![K-AI-3](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5101579161/p237487.png)

5.  Click **Deploy**.


## Access AI Dashboard

You can access AI Dashboard by using three methods. The following table compares the advantages and disadvantages of these methods.

**Comparison of three access methods**

|Access method|Advantage|Disadvantage|
|-------------|---------|------------|
|Sshuttle|The configuration is simple.|The connection may be unstable.|
|SSL VPN|The connection is stable.|Has requirements for the CIDR block of the cluster. The configuration is complicated.|
|Internet-facing Ingress|The configuration is simple.|Exposes the cluster to security risks.|

**Method 1: Use sshuttle to access AI Dashboard**

1.  Run the following command to install sshuttle in the macOS operating system.

    In this example, install sshuttle 1.0.5 in the macOS operating system.

    ```
    brew install sshuttle@1.0.5
    ```

    For more information about how to install sshuttle in other operating systems, see [sshuttle documentation](https://github.com/sshuttle/sshuttle).

2.  Run the following command to run sshuttle as a proxy server. You must specify the IP address of the jump server that allows SSH access and the IP address of the cluster node that you want to access.

    ```
    sshuttle -r jack@39.96.XX.XX 192.168.100.0/24 -vv
    ```

3.  In the address bar of your browser, enter the address of AI Dashboard, for example, `http://192.168.100.70:30001/`.


**Method 2: Use an SSL VPN to access AI Dashboard**

You can configure SSL-VPN on a VPN gateway to remotely access AI Dashboard from a client. For more information, see [Use SSL-VPN in the classic network](/intl.en-US/Best Practices/Use SSL-VPN in the classic network/Access cloud resources in a classic network from a Mac client.md).

If you use the macOS operating system, you must use Researcher to access AI Dashboard and make sure that Researcher and AI Dashboard belong to the same CIDR block. For more information, see [Step 4: Configure the client](/intl.en-US/Best Practices/Use SSL-VPN in the classic network/Access cloud resources in a classic network from a Mac client.mdsection_qdz_j4g_xdb).

**Method 3: Use an Internet-facing Ingress to access AI Dashboard**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Services and Ingresses** \> **Ingresses**.

5.  Select the **kube-ai** namespace.

    ![K-AI-4](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7203717161/p237961.png)

6.  Click **Create**. In the **Create** dialog box, configure **Name** and **Domain**. Select **ai-dashboard-admin-ui** for **Service** and click **Create**.

7.  After the Ingress is created, modify the callback URL that is associated with RAM.

    **Note:** You can find the callback URL in the **Rule** column of the new Ingress. The callback URL is in the following format: http://<ai\_dashboar\_host\>:<ai\_dashbaord\_port\>/login/aliyun.

    1.  Log on to the [RAM console](https://ram.console.aliyun.com/) by using your Alibaba Cloud account.

    2.  In the left-side navigation pane, click **OAuth Applications**.

    3.  Click the **Enterprise Applications** tab. Click the **application** that is associated with the administrator ID in [3](#step_20t_ctw_pf1) of Step 2: Install AI Dashboard.

    4.  Click **Edit Basic Information**.

    5.  In the **Create Application** panel, modify the callback URL in the **Callback URL** field.

8.  Return to the Ingresses page. Select the Ingress that you created and click the URL in the **Rule** column to access AI Dashboard.

    **Note:** If other users want to access AI Dashboard, you can provide the users with the Ingress URL obtained in [5](#step_e02_ens_x1g).



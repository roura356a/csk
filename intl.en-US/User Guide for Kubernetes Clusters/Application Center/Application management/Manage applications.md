---
keyword: [update applications, roll back applications, check applications, delete applications]
---

# Manage applications

You can view the deployment status and changes of all Kubernetes resources related to an application deployed in Application Center in the Container Service for Kubernetes \(ACK\) console. After an application is deployed in Application Center, you can view, update, and delete the application. This topic describes how to view, update, and delete an application in Application Center.

## View an application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Application Center page, click the ![View Application](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/1468128261/p260333.png) icon to the left side of the application that you want to view. You can view information about the application, including the **cluster**, **namespace**, **health status**, **deployment status**, and **deployed version**.


## Update an application

**Note:** Before you update an application, update the source code:

-   If you use a template from a Git repository to deploy an application, make sure that the latest template is pushed to the Git repository.
-   If you use an orchestration template to deploy an application, make sure that the orchestration template is updated. For more information, see [Modify an orchestration template](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/Template management/Modify an orchestration template.md).

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Application Center page, find the application that you want to update and click **Create Change** in the **Actions** column.

4.  In the **Deployment Status** section, you can find that **Latest Version Not Deployed** is displayed in the **Deployment Status** column. Click **Next**.

    **Note:** In the **Deployment Status** section, you can click **Compare With Latest Version** in the **Deployed Version** column to see the differences between the latest version and the current version.

5.  In the **Release Configurations** step, set the parameters.

    1.  In the **Release Configurations** section, set the release type. You can select **Full Release** or **Canary Release**.

    2.  In the **Select Cluster** section, click **Add** to the left side of the cluster you want to select. The added cluster appears in the **Cluster** section. Then, select the **namespace** where you want to deploy the new application version in the **Cluster** section.

    3.  Click **Release Now**.

6.  Click OK in the **publish confirmation** dialog box.

    You can see the progress of publishing applications in the **publishing log** configuration page.


## Roll back an application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Application Center page, click the name of the application that you want to roll back.

4.  On the application details page, click **History Versions** in the upper-right corner.

5.  In the **History** panel, click **Roll Back** to the right side of the version to which you want to roll back the application. In the message that appears, click **OK**.


## Delete an application

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  On the Application Center page, find the application that you want to delete and click **Delete** in the **Actions** column.

4.  In the message that appears, click **OK**.


**Related topics**  


[Create an application from an orchestration template](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application management/Create an application from an orchestration template.md)

[Create an application by using a Git repository](/intl.en-US/User Guide for Kubernetes Clusters/Application Center/Application management/Create an application by using a Git repository.md)


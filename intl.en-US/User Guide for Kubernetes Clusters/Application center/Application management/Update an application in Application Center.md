---
keyword: [Application Center, update an application]
---

# Update an application in Application Center

You can update an application in the Container Service for Kubernetes \(ACK\) console.

-   If you use a template from a Git repository to update an application, make sure that the template in the Git repository is updated to the latest version.
-   If you use an ACK orchestration template to update an application, make sure the ACK orchestration template in the ACK console is updated to the latest version. For more information, see [Update an orchestration template](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/Template management/Update an orchestration template.md).

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  Click the name of the application to go to the details page of the application.

4.  Click **REFRESH** and then click **Hard Refresh** to obtain the latest template.

    In the following figure, the application is in the **OutOfSync** state.

    ![OutOfSync](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2671259161/p180508.png)

5.  In the upper-right corner of the details page, click **SYNC**.

6.  In the dialog box that appears, select the resource objects that you want to deploy and click **SYNCHRONIZE**.


If the application changes to the **Sync OK** state, the application is updated.

![Sync OK](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2671259161/p180509.png)


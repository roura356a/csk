---
keyword: [Application Center, roll back an application]
---

# Roll back an application in Application Center

You can roll back an application created in Application Center to an earlier version and view details about each application version. This topic describes how to roll back an application.

[Deploy an application in Application Center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Application management/Deploy an application in Application Center.md)

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  Click the name of the application that you want to roll back. On the details page of the application, click **HISTORY AND ROLLBACK**.

4.  On the page that appears, find the application version to which you want to roll back, click ![More](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4955359951/p128515.png) in the upper-right corner of the page and click **Rollback**.

5.  In the Rollback application message, click **OK**.

    After the rollback is complete, the application changes to the **OutOfSync** state.


You can update templates in source repositories and deploy new application versions to a cluster. For more information, see [Update an application in Application Center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Application management/Update an application in Application Center.md).

**Related topics**  


[View applications in Application Center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Application management/View applications in Application Center.md)


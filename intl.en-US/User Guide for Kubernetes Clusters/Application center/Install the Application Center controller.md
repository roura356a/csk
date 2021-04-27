---
keyword: [Application Center, install the Application Center controller, K8s]
---

# Install the Application Center controller

Before you deploy an application in Application Center, you must install the Application Center controller. The Application Center controller enables Application Center. After the controller is installed, you can deploy applications and manage application versions in Application Center. This topic describes how to install the Application Center controller.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Multi-cluster** \> **Application Center**.

3.  Install the Application Center controller.

    Select the cluster in which you want to install the controller and click **Install**. It requires about three minutes to complete the installation process.

4.  Add a cluster to Application Center.

    By default, the system adds the cluster where the Application Center controller is installed to Application Center. After you add a cluster, you can deploy applications to the added cluster in Application Center. The Application Center controller is installed in only the cluster that you select when you initialize Application Center. You do not need to install the controller in the added clusters. This provides an easy method for you to deploy and manage applications in multiple clusters.

    After you add a cluster, click **Get Started** to deploy applications.


**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Overview.md)

[Deploy an application in Application Center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Application management/Deploy an application.md)


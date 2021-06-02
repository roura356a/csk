---
keyword: [Xid messages that indicate GPU errors, Kubernetes event center, monitoring and alerting, exceptions]
---

# Use the Kubernetes event center to monitor GPU errors

This topic describes how to use the Kubernetes event center to monitor GPU Xid messages and configure alerts for Xid messages that indicate GPU errors. This provides diagnostic information that can be used for debugging NVIDIA driver errors.

-   [A managed Kubernetes cluster with GPU-accelerated nodes is created](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a managed Kubernetes cluster with GPU-accelerated nodes.md) or [Create a dedicated Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a dedicated Kubernetes cluster with GPU-accelerated nodes.md).
-   [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md)

An Xid message is an error report from the NVIDIA driver that is printed to the kernel log or event log of the operating system. Xid messages indicate that a general GPU error occurred. In most cases, the general GPU error occurs due to improper driver programming over the GPU or due to corruption of the commands sent to the GPU. The messages can be indicative of a hardware problem, an NVIDIA software problem, or a user application problem.

GPU drivers are prone to Xid errors. You can use the Kubernetes event center to monitor Xid errors and configure alerts. This allows you to identify and troubleshoot issues at the earliest opportunity.

## Procedure

1.  Log on to the Log Service console. In the Log Application section, find and click K8s Event Center.

    For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

2.  In the left-side navigation pane of the K8s Event Center page, select the cluster that you want to manage and click **Event Overview**.

    On the Event Overview tab, you can view Xid messages and triggered alerts.

3.  In the left-side navigation pane, select the cluster that you want to manage, and click **Alert Configuration**.

4.  Click **Add Notification Method**. In the **Add Notification Method** panel, configure the notification method and click **OK**.

    You can select a notification method to receive alerts through SMS, emails, or DingTalk notifications. You can also customize the alert content. The following figure shows how to enable SMS alerting.

    ![SMS](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1045359951/p129869.png)

5.  After you configure the notification method, click **Modify** in the upper-right corner of the Events section. In the **Kubernetes GPU Xid Alerts** card, turn on the switch and select **SMS** from the drop-down list.****

6.  On the Events page, click **Save**.

    After an alert is triggered, you can receive a message from Alibaba Cloud.



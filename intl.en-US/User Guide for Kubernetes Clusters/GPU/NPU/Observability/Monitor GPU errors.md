---
keyword: [Xid messages that indicate GPU errors, Kubernetes Event Center, monitoring and alerting, exceptions]
---

# Monitor GPU errors

This topic describes how to use Kubernetes Event Center to monitor GPU-accelerated instances and configure alerts for Xid messages that indicate GPU errors. This provides diagnostic information that can be used for debugging reported NVIDIA driver errors.

-   A managed or dedicated GPU cluster is created. For more information, see [Create a managed GPU cluster](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a managed Kubernetes cluster with GPU-accelerated nodes.md) or [Create a dedicated Kubernetes cluster with GPU-accelerated nodes](/intl.en-US/User Guide for Kubernetes Clusters/GPU/NPU/Create heterogeneous computing clusters/Create a dedicated Kubernetes cluster with GPU-accelerated nodes.md).
-   [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md)

An Xid message is an error report from the NVIDIA driver. Such a report is printed to the kernel log or event log of the operating system. An Xid message indicates that a general GPU error occurred. In most cases, a GPU error occurs due to improper driver programming over the GPU or due to the corruption of the commands sent to the GPU. You can use Xid messages to identify hardware, NVIDIA software, or application issues.

GPU drivers are prone to Xid errors. You can use Kubernetes Event Center to monitor Xid errors and configure alerts. This allows you to identify and troubleshoot issues at the earliest opportunity.

## Procedure

1.  Log on to the Log Service console. In the Log Application section, click the K8s Event Center card.

    For more information, see [Create and use a Kubernetes event center](/intl.en-US/Application/K8s Event Center/Create and use a Kubernetes event center.md).

2.  In the left-side navigation pane under K8s Event Center, select the cluster that you want to manage, and click **Event Overview**.

    On the Event Overview tab, you can view Xid messages and triggered alerts.

3.  In the left-side navigation pane, select the cluster that you want to manage, and click **Alert Configuration**.

4.  Click **Add Notification Method**. In the **Add Notification Method** panel, configure the notification method, and click **OK**.

    You can select a notification method to receive alerts through text messages, emails, or DingTalk notifications. You can also customize the alert content. The following figure shows how to enable SMS alerting.

    ![Text messages](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1045359951/p129869.png)

5.  After you configure the notification method, click **Modify** in the upper-right corner of the Events section. In the **Kubernetes GPU Xid Alerts** card, turn on the switch, and select **SMS** from the drop-down list.

6.  In the Events section, click **Save**.

    After an alert is triggered, you receive a text message from Alibaba Cloud.



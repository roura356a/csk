# Use Runtime Security to monitor ACK clusters and configure alerts

Runtime Security monitors clusters of Alibaba Cloud Container Service for Kubernetes \(ACK\) and triggers alerts upon security events. Alerts are triggered upon the following security events: attacks by viruses or malware in containers and hosts, intrusions into containers, container escapes, and high-risk operations on containers. This topic describes how to use Runtime Security to monitor ACK clusters and configure alerts.

-   An ACK cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   Security Center is activated. For more information, see [Purchase Security Center](/intl.en-US/Pricing/Purchase Security Center.md).
-   If your account is a Resource Access Management \(RAM\) user, you must grant the RAM user the AliyunYundunSASReadOnlyAccess permission.

Cloud-native applications are deployed in containers after they pass the authentication, authorization verification, and admission control of the API server. However, these applications are not protected from security risks. To resolve this issue, you can use Runtime Security. Runtime Security monitors applications and triggers alerts upon security events. Runtime Security is integrated with Security Center to detect vulnerabilities and raise alerts. This allows cluster administrators to monitor applications and receive alerts upon the following security events: attacks by viruses or malware in containers and hosts, intrusions into containers, container escapes, and high-risk operations on containers. You can view and manage alerts on the details page of an ACK cluster.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, click the name of a cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Security** \> **Runtime Security**. On the Runtime Security page, you can view the monitoring information and alerts.

    -   The security condition of the cluster appears on the Runtime Security page. The security condition includes security detection, node status, and defense capabilities.
        -   Security detection: shows security events that have triggered alerts. Click **More** to navigate to the Security Center console for details.
        -   Node status: shows healthy nodes and unhealthy nodes.
        -   Defense Capabilities: shows pending alerts, the time when the anti-virus database was last updated, and the time when system vulnerabilities were last scanned.
    -   On the Runtime Security page, click the **Alerts** tab to view the triggered alerts in real time. Alerts are triggered upon the following security events: attacks by viruses or malware in containers and hosts, intrusions into containers, container escapes, and high-risk operations on containers. For more information, see [Overview](/intl.en-US/Threat Detection/Events/Overview.md).
        -   On the Alerts tab, find the alert that you want to manage, and click **Manage** in the **Actions** column. In the dialog box that appears, add this alert to the whitelist or ignore this alert.
        -   On the Alerts tab, find the alert that you want to view, and click **Details** in the **Actions** column. On the details page, you can view the information about this alert, such as the time when the event occurred, affected assets, and process IDs. On the **Details** page, click the **Diagnosis** tab. On the Diagnosis tab, you can enable automatic attack tracing and view the raw data.
    -   On the Runtime Security page, click the **Vulnerabilities** tab to view vulnerabilities identified by Common Vulnerabilities and Exposures \(CVE\) on the cluster nodes.

        On the Vulnerabilities tab, find the vulnerability that you want to fix, and click **Fix** in the **Actions** column. On the page that appears, you can fix the vulnerability and repair the affected assets.

        **Note:** The vulnerabilities that are revealed on the Vulnerabilities tab include: Linux software vulnerabilities, Windows system vulnerabilities, web content management system \(WCMS\) vulnerabilities, application vulnerabilities, and emergency vulnerabilities. For more information, see the following topics:

        -   [Linux software vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/Linux software vulnerabilities.md)
        -   [Windows system vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/Windows system vulnerabilities.md)
        -   [Web-CMS vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/Web-CMS vulnerabilities.md)
        -   [Application vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/Application vulnerabilities.md)
        -   [Urgent vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/Emergency vulnerability detection.md)


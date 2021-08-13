---
keyword: [runtime, security monitoring]
---

# Use the runtime security feature to monitor ACK clusters and configure alerts

The runtime security feature provides the monitoring of Container Service for Kubernetes \(ACK\) clusters and can trigger alerts upon security events, such as malicious image startups, attacks by viruses or malware in containers and hosts, intrusions into containers, container escapes, and high-risk operations on containers. This topic describes how to use the runtime security feature to monitor ACK clusters and configure alerts.

-   A Kubernetes cluster is created. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md).
-   Security Center is activated. For more information, see [Purchase Security Center](/intl.en-US/Pricing/Purchase Security Center.md).
-   If you use a Resource Access Management \(RAM\) user, you must attach the AliyunYundunSASReadOnlyAccess policy to the RAM user.

Cloud-native applications are deployed in containers after they pass the authentication and admission control of the API server. However, in accordance with the zero trust principle for application security, monitoring and alerting are required to ensure the security of application runtimes. The runtime security feature is integrated with Security Center to detect vulnerabilities and raise alerts. This allows cluster administrators to monitor applications and receive alerts upon security events, such as attacks by viruses or malware in containers and hosts, intrusions into containers, container escapes, and high-risk operations on containers. Alerts are sent to the cluster details page in the ACK console in real time. You can view and handle the alerts based on the information displayed on the page.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Security** \> **Runtime Security**. On the Runtime Security page, you can view the monitoring information and alerts.

    -   The Runtime Security page shows security status from the following aspects:
        -   Security detection: shows security events that have triggered alerts. Click **More** to go to the Security Center console for details.
        -   Node status: shows healthy nodes and unhealthy nodes.
        -   Defense capabilities: shows pending alerts, the time when the anti-virus database of the scanning agent was last updated, and the time when the system vulnerability scan was last performed.
    -   On the Runtime Security page, click the **Alerts** tab to view the triggered alerts in real time. Alerts are triggered upon security events, such as attacks by viruses or malware in containers and hosts, intrusions into containers, container escapes, and high-risk operations on containers. For more information, see [Overview](/intl.en-US/Threat Detection/Events/Overview.md).
        -   On the **Alerts** tab, find the alert that you want to manage and click **Manage** in the **Actions** column. In the dialog box that appears, you can add this alert to the whitelist or ignore this alert.
        -   On the **Alerts** tab, find the alert that you want to view and click **Details** in the **Actions** column. On the details page, you can view information about this alert, such as the time when the event occurred, affected assets, and process IDs. On the **Details** page, click the **Diagnosis** tab. On the Diagnosis tab, you can enable automatic attack tracing and view the raw data.
    -   On the Runtime Security page, click the **Vulnerabilities** tab to view Common Vulnerabilities and Exposures \(CVE\) vulnerabilities on the cluster nodes.

        On the Vulnerabilities tab, find the vulnerability that you want to fix, and click **Repair** in the **Actions** column. On the page that appears, you can fix the vulnerability and repair the affected assets.

        **Note:** The vulnerabilities displayed on the Vulnerabilities tab include: Linux software vulnerabilities, Windows system vulnerabilities, web content management system \(WCMS\) vulnerabilities, application vulnerabilities, and emergency vulnerabilities. For more information, see the following topics:

        -   [View and handle Linux software vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/View and handle Linux software vulnerabilities.md)
        -   [View and handle Windows system vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/View and handle Windows system vulnerabilities.md)
        -   [View and handle Web-CMS vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/View and handle Web-CMS vulnerabilities.md)
        -   [View and handle application vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/View and handle application vulnerabilities.md)
        -   [View and handle urgent vulnerabilities](/intl.en-US/Precautions/Vulnerabilities/View and handle urgent vulnerabilities.md)


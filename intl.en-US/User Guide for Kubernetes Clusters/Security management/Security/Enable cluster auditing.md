---
keyword: [manually enable cluster auditing, audit log reports, cluster auditing, disable cluster auditing]
---

# Enable cluster auditing

In a Kubernetes cluster, kube-apiserver collects audit logs that help administrators track operations performed by different users. This plays an essential role in security and maintenance of the cluster. This topic describes how to configure parameters for cluster auditing, how to enable Log Service to collect and analyze audit logs, how to set custom alert rules for audit logs based on your needs, and how to disable cluster auditing.

## Configure parameters for cluster auditing

By default, the **Enable Log Service** check box is selected when you create a cluster. This indicates that kube-apiserver automatically collects audit logs for the cluster. The following table describes the parameters of cluster auditing.

**Note:** Log on to a master node. You can find the configuration file of kube-apiserver in the following path: /etc/kubernetes/manifests/kube-apiserver.yaml.

|Parameter|Description|
|---------|-----------|
|audit-log-maxbackup|A maximum of 10 audit log files can be retained.|
|audit-log-maxsize|The maximum size of an audit log file is 100 MB.|
|audit-log-path|The log files are stored in the /var/log/kubernetes/kubernetes.audit path.|
|audit-log-maxage|Audit log files are retained for a maximum of seven days.|
|audit-policy-file|The path of the audit policy file is /etc/kubernetes/audit-policy.yml.|

Log on to a master node. You can find the audit policy file in the following path: /etc/kubernetes/audit-policy.yml. The audit policy file contains the following content:

```
apiVersion: audit.k8s.io/v1beta1 # This is required.
kind: Policy
# Don't generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # The following requests were manually identified as high-volume and low-risk,
  # so drop them.
  - level: None
    users: ["system:kube-proxy"]
    verbs: ["watch"]
    resources:
      - group: "" # core
        resources: ["endpoints", "services"]
  - level: None
    users: ["system:unsecured"]
    namespaces: ["kube-system"]
    verbs: ["get"]
    resources:
      - group: "" # core
        resources: ["configmaps"]
  - level: None
    users: ["kubelet"] # legacy kubelet identity
    verbs: ["get"]
    resources:
      - group: "" # core
        resources: ["nodes"]
  - level: None
    userGroups: ["system:nodes"]
    verbs: ["get"]
    resources:
      - group: "" # core
        resources: ["nodes"]
  - level: None
    users:
      - system:kube-controller-manager
      - system:kube-scheduler
      - system:serviceaccount:kube-system:endpoint-controller
    verbs: ["get", "update"]
    namespaces: ["kube-system"]
    resources:
      - group: "" # core
        resources: ["endpoints"]
  - level: None
    users: ["system:apiserver"]
    verbs: ["get"]
    resources:
      - group: "" # core
        resources: ["namespaces"]
  # Don't log these read-only URLs.
  - level: None
    nonResourceURLs:
      - /healthz*
      - /version
      - /swagger*
  # Don't log events requests.
  - level: None
    resources:
      - group: "" # core
        resources: ["events"]
  # Secrets, ConfigMaps, and TokenReviews can contain sensitive & binary data,
  # so only log at the Metadata level.
  - level: Metadata
    resources:
      - group: "" # core
        resources: ["secrets", "configmaps"]
      - group: authentication.k8s.io
        resources: ["tokenreviews"]
  # Get repsonses can be large; skip them.
  - level: Request
    verbs: ["get", "list", "watch"]
    resources:
      - group: "" # core
      - group: "admissionregistration.k8s.io"
      - group: "apps"
      - group: "authentication.k8s.io"
      - group: "authorization.k8s.io"
      - group: "autoscaling"
      - group: "batch"
      - group: "certificates.k8s.io"
      - group: "extensions"
      - group: "networking.k8s.io"
      - group: "policy"
      - group: "rbac.authorization.k8s.io"
      - group: "settings.k8s.io"
      - group: "storage.k8s.io"
  # Default level for known APIs
  - level: RequestResponse
    resources:
      - group: "" # core
      - group: "admissionregistration.k8s.io"
      - group: "apps"
      - group: "authentication.k8s.io"
      - group: "authorization.k8s.io"
      - group: "autoscaling"
      - group: "batch"
      - group: "certificates.k8s.io"
      - group: "extensions"
      - group: "networking.k8s.io"
      - group: "policy"
      - group: "rbac.authorization.k8s.io"
      - group: "settings.k8s.io"
      - group: "storage.k8s.io"
  # Default level for all other requests.
  - level: Metadata
```

**Note:**

-   Requests are not logged upon reception. Requests are logged only after response headers are sent.
-   The following types of requests are not logged: watch requests by kube-proxy, GET requests by kubelet and system:nodes for node resources, operations for endpoint resources by kube components in the kube-system namespace, and GET requests by kube-apiserver for namespace resources.
-   Requests with read-only URLs that match /healthz\*, /version\*, or /swagger\* are not logged.
-   Requests for Secrets, ConfigMaps, and TokenReview resources are logged at the metadata level because these resources may contain sensitive information or binary files. Cluster auditing at the metadata level only collects request metadata, such as request users, timestamps, request resources, and actions. The information about the request body or the response body is not logged.
-   Requests on sensitive resources such as authentication, role-based access control \(RBAC\), certificates, auto scaling, and storage resources are logged, including the request body and the response body.

## View audit log reports

Container Service for Kubernetes \(ACK\) provides three audit log reports for each cluster. You can find the following information in these reports:

-   Important operations performed by users and system components on the cluster.
-   The source IP addresses of these operations and the regional distribution of these IP addresses.
-   The details of operations on each type of resources.
-   The details of operations by Resource Access Management \(RAM\) users.
-   Details of important operations, such as container logons, Secret retrieval, and resource deletion.

**Note:**

-   By default, the Enable Log Service check box is selected when you create a cluster. This indicates that cluster auditing is automatically enabled. For more information about the billing method of Log Service, see [Billing rules](#section_pq5_y0q_dgd). If Log Service is not activated, see [Enable cluster auditing](#section_olz_426_8mm).
-   Do not modify audit log reports. If you want to customize reports, log on to the [Log Service console](https://sls.console.aliyun.com/) to create new reports.

You can use one of the following methods to view audit log reports:

-   Log on to the [ACK console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Clusters**. On the Clusters page, find the cluster that you want to view, and choose **More** \> **Cluster Auditing** in the **Actions** column.
-   Log on to the [ACK console](https://cs.console.aliyun.com). In the left-side navigation pane, click **Clusters**. On the Clusters page, click the name of the cluster that you want to view. In the left-side navigation pane of the details page of the cluster, choose **Security** \> **Cluster Auditing**.

## Overview of audit log reports

The Cluster Auditing page displays audit log reports on three tabs: Overview, Operations Overview, and Operation Details.

-   Overview

    This report provides an overview of the events in the cluster and the details of important events, such as access from the Internet, command executions, resource deletion, and Secret retrieval.

    ![Overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6914005061/p133068.png)

    **Note:** By default, the report displays statistics of the last seven days. You can specify a time period and view the statistics collected during the period. You can filter the statistics by namespace, RAM user ID, and status code. You can also select one or more items to filter the statistics.

-   Operations Overview

    This report provides statistics about common operations on computing resources, network resources, and storage resources in the cluster. The operations include creating resources, updating resources, deleting resources, and accessing resources. The following information is displayed:

    -   Computing resources include Deployment, StatefulSet, CronJob, DaemonSet, Job, and Pod.
    -   Network resources include Service and Ingress.
    -   Storage resources include ConfigMap, Secret, and PersistentVolumeClaim.
    ![Operations Overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6914005061/p133069.png)

    **Note:**

    -   By default, the report displays statistics of the last seven days. You can specify a time period and view the statistics collected during the period. You can filter the statistics by namespace and RAM user ID. You can also select one or more items to filter the statistics.
    -   To view details of the operations on a resource, go to the Operation Details report.
-   Operation Details

    This report provides operation details on a specific resource type. You can specify a resource type to query operation details in real time. The report contains the total number of operations, distribution of namespaces, operation success rate, temporal order of operations, and other operation details.

    ![Operation Details](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7914005061/p133070.png)

    **Note:**

    -   To query operations about a CustomResourceDefinition \(CRD\) resource registered in Kubernetes or a resource that is not listed in the report, enter the plural form of the resource name. For example, to query operations about a CRD resource named AliyunLogConfig, enter AliyunLogConfigs.
    -   By default, the report displays statistics of the last seven days. You can specify a time period and view the statistics collected during the period. You can filter the statistics by namespace, RAM user ID, and status code. You can also select one or more items to filter the statistics.

## View detailed log data

To customize queries or analyze audit logs, go to the Log Service console and view detailed log data.

**Note:** By default, Log Service retains audit logs in a Logstore for 30 days. For information about how to change the log retention period, see [Manage a Logstore](/intl.en-US/Data Collection/Preparation/Manage a Logstore.md).

1.  Log on to the [Log Service console](https://sls.console.aliyun.com/#/sls).

2.  Find the project of the cluster and click the project name.

3.  Find Logstore audit-$\{clustered\}, click the ![Search & Analysis icon](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7914005061/p75381.png) icon, and then select **Search & Analysis** to view the audit logs.

    **Note:**

    -   During the cluster creation process, a Logstore named `audit-${clustereid}` is automatically created under the project.
    -   By default, indexes are set up for the Logstore. Do not modify the indexes.

You can query audit logs by using the following methods:

-   To query the operations performed by a RAM user, enter the RAM user ID, and click **Search & Analyze**.
-   To query the operations performed on a resource, enter the resource name, and click **Search & Analyze**.
-   To filter out the operations performed by system components, enter `NOT user.username: node NOT user.username: serviceaccount NOT user.username: apiserver NOT user.username: kube-scheduler NOT user.username: kube-controller-manager`, and click **Search & Analyze**.

For more information about how to query logs, see [Query methods](/intl.en-US/Index and query/Overview.md).

## Configure an alert rule

Log Service enables you to configure alert rules to monitor the operations that are performed on specific resources in real time. Available notification methods includeDingTalk chatbot webhooks,custom webhooks, and Alibaba Cloud Message Center. For more information, see [Configure an alert rule for Log Service](/intl.en-US/Query and visualization/Alarm/Overview.md).

**Note:** For more information about how to query audit logs, see the query statements in audit reports. You can perform the following steps to view query statements: On the project details page, click the **Dashboard** icon in the left-side navigation pane. Select a dashboard to go to the details page. Select a chart, click the More icon, and then click **View Analysis Details**.

Example 1: Alerts upon command execution on containers

To monitor command execution on containers, alerts must be sent at the earliest opportunity when a user attempts to log on to a container or run commands on a container. The alert notification must include the following information: the container that the user logs on to, the commands, the user name, the event ID, the operation time, and the source IP address.

-   Sample query statement:

    ```
    verb : create and objectRef.subresource:exec and stage:  ResponseStarted | SELECT auditID as "event ID", date_format(from_unixtime(__time__), '%Y-%m-%d %T' ) as "execution time",  regexp_extract("requestURI", '([^\?] *)/exec\?.*', 1)as "Resource",  regexp_extract("requestURI", '\?(.*)', 1)as "Command" ,"responseStatus.code" as "Status code",
     CASE 
     WHEN "user.username" ! = 'kubernetes-admin' then "user.username"
     WHEN "user.username" = 'kubernetes-admin' and regexp_like("annotations.authorization.k8s.io/reason", 'RoleBinding') then regexp_extract("annotations.authorization.k8s.io/reason", ' to User "(\w+)"', 1)
     ELSE 'kubernetes-admin' END  
     as "Username", 
    CASE WHEN json_array_length(sourceIPs) = 1 then json_format(json_array_get(sourceIPs, 0)) ELSE  sourceIPs END
    as "Source IP address" limit 100
    ```

-   The conditional expression is `Event =~ ". *"`.

Example 2: Alerts upon failed Internet access to the API server

To prevent attacks on a cluster based on connections over the Internet, monitor the number of connections from the Internet and the connection failure rate. Alerts are sent if the number of connections and the connection failure rate exceed the specified thresholds. The alert notification must include the following information: the source IP address, the region of the source IP address, and whether the source IP address is risky. In the following query statement, alerts are generated if the number of connections from the Internet exceeds 10 and the connection failure rate exceeds 50%.

-   Sample query statement:

    ```
    * | select ip as "Source IP address", total as "Number of connections", round(rate * 100, 2) as "Connection failure rate", failCount as "Number of invalid connections", CASE when security_check_ip(ip) = 1 then 'yes' else 'no' end  as "Whether the IP address is risky",  ip_to_country(ip) as "Country", ip_to_province(ip) as "Province", ip_to_city(ip) as "City", ip_to_provider(ip) as "ISP" from (select CASE WHEN json_array_length(sourceIPs) = 1 then json_format(json_array_get(sourceIPs, 0)) ELSE  sourceIPs END
    as ip, count(1) as total,
    sum(CASE WHEN "responseStatus.code" < 400 then 0 
    ELSE 1 END) * 1.0 / count(1) as rate,
    count_if("responseStatus.code" = 403) as failCount
    from log  group by ip limit 10000) where ip_to_domain(ip) ! = 'intranet'  having "Number of connections" > 10 and "Connection failure rate × 100" > 50 ORDER by "Number of connections" desc limit 100
    ```

-   The conditional expression is `Source IP address =~ ". *"`.

## Enable cluster auditing

By default, the **Enable Log Service** check box is selected when you create a cluster. In this case, kube-apiserver automatically collects audit logs for the cluster. If cluster auditing is disabled, perform the following steps to enable this feature:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, choose **Clusters** \> **Clusters**. On the Clusters page, find the cluster for which you want to enable cluster auditing, and click **Manage**.

5.  In the left-side navigation pane of the details page of the cluster, choose **Security** \> **Cluster Auditing**. If cluster auditing is disabled, you are prompted to enable this feature.

6.  Click **Enable Cluster Auditing Now**. **Select an existing project** or **create a project**, and then click **OK**.

    If the following page appears, cluster auditing is enabled.

    ![Auditing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7914005061/p133071.png)


## Disable cluster auditing

You can perform the following steps to disable cluster auditing:

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page of the cluster, choose **Security** \> **Cluster Auditing**.

5.  In the upper-right corner, click **Disable Cluster Auditing**.


## Billing rules

-   On the Overview tab of the Bills page, you can view the billing information about audit logs. For more information, see [Bills](https://expense.console.aliyun.com/#/consumption/outline).
-   For more information about the billing method of cluster auditing, see [Pay-as-you-go](/intl.en-US/Pricing/Pay-as-you-go.md).

## Support for third-party logging services

You can find the source log file in the /var/log/kubernetes/kubernetes.audit path of a master node. This file is in standard JSON format. When you create a cluster, you can specify a third-party logging service to collect and retrieve logs.


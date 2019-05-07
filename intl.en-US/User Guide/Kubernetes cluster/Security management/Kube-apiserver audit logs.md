# Kube-apiserver audit logs {#concept_svk_cgf_2fb .concept}

In a Kubernetes cluster, apiserver audit logs are important for cluster Operation & Maintenance \(O&M\) because they record daily operations of different users. This topic describes how to configure the apiserver audit logs of an Alibaba Cloud Kubernetes cluster, and how to collect and analyze audit logs through Log Service, and how to customize audit log alarm rules.

## Configurations of apiserver audit logs {#section_edv_5gf_2fb .section}

The apiserver audit function is enabled by default when you create a Kubernetes cluster. Relevant parameters and description are as follows:

**Note:** Log on to the Master node, and the directory of the apiserver configuration files is /etc/kubernetes/manifests/kube-apiserver.yaml.

|Configuration|Description|
|-------------|-----------|
|audit-log-maxbackup|The maximum fragment of audit logs stores 10 log files.|
|audit-log-maxsize|The maximum size of a single audit log is 100 MB.|
|audit-log-path|The audit log output path is /var/log/kubernetes/kubernetes.audit.|
|audit-log-maxage|The longest storage period of audit logs is seven days.|
|audit-policy-file|Configuration policy file of audit logs. The directory is /etc/kubernetes/audit-policy.yml.|

Log on to the Master node machine. The directory of the audit log configuration policy file is /etc/kubernetes/audit-policy.yml. The content of the file is as follows:

```
apiVersion: audit.k8s.io/v1beta1 # This is required.
kind: Policy
# We recommend that you do not generate audit events for all requests in RequestReceived stage.
omitStages:
  - "RequestReceived"
rules:
  # The following requests are manually identified as high-volume and low-risk.
  # Therefore, we recommend that you drop them.
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
  # We recommend that you do not log these read-only URLs.
  - level: None
    nonResourceURLs:
      - /healthz*
      - /version
      - /swagger*
  # We recommend that you do not log events requests.
  - level: None
    resources:
      - group: "" # core
        resources: ["events"]
  # Secrets, ConfigMaps, and TokenReviews can contain sensitive and binary data.
  # Therefore, they are logged only at the Metadata level.
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
  # Default level for known APIs.
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

-   Logs are not recorded immediately after requests are received. Log recording starts only after the response body header is sent.
-   The following requests or operations are not audited: redundant kube-proxy watch requests, GET requests from kubelet and system:nodes for nodes, operations performed on endpoints by kube components in the kube-system, and GET requests from the apiserver for namespaces.
-   Read-only urls such as /healthz\*, /version\*, and /swagger\* are not audited.
-   Logs of interfaces of secrets, configmaps, and tokenreviews are set to the metadata level because they might contain sensitive information or binary files. For logs of this level, only the user, timestamp, request resources, and request actions of the request event are audited. The request body and the response body are not audited.
-   For sensitive interfaces such as authentication, rbac, certificates, autoscaling, and storage, the corresponding request bodies and response bodies are audited according to the read and write requests.

## View audit log reports {#section_ry3_pnf_qgb .section}

A Kubernetes cluster that runs on Alibaba Cloud Container Service has three audit log reports that provide the following information:

-   Operations performed by all users and system components on the cluster
-   The source IP address of each operation, the area to which a source IP addresses belongs, and the source IP address distribution
-   Detailed operation charts of all resources
-   Operation charts of each sub-account
-   Charts of important operations such as logging on to a container, accessing a secret, and removing resources

**Note:** 

-   For Kubernetes clusters created after January 13, 2019, if you active Log Service for the clusters, the system automatically enables audit log report functions. If audit log report functions are disabled for a Kubernetes cluster, see[Manually enable audit log report functions](#).
-   We recommend that you do not modify audit log reports. If you want to customize audit log reports, you can create new reports in the[Log Service console](https://partners-intl.console.aliyun.com/#/sls).

You can access audit log reports by using either of the following two methods:

-   Log on to the[Container Service console](https://partners-intl.console.aliyun.com/#/cs). In the action column of the target cluster, choose**More** \> **Cluster Audit**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/155719462037908_en-US.png)

-   Log on to the[Container Service console](https://partners-intl.console.aliyun.com/#/cs).Click the target cluster name, and then click**Cluster Audit**in the left-side navigation pane.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/155719462037910_en-US.png)


## Audit log report overview {#section_hkj_bcy_ngb .section}

The following three apiserver audit log reports are available: Audit Center Overview, Resource Operation Overview, and Resource Operation Detail.

-   Audit Center Overview

    This report displays an overview of the Kubernetes cluster events and the detailed information about important events, such as public network visits, command execution, resource removal, and secret visits.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/155719462037911_en-US.png)

    **Note:** By default, this report displays statistics for one week. You can customize a statistics time range. In addition, you can filter events by specifying one or multiple factors, such as a namespace, a sub-account ID, and a status code.

-   Resource Operation Overview

    This report displays the operation statistics information about computing resources, network resources, and storage resources of a Kubernetes cluster. Operations include creation, update, removal, and access.

    -   Computing resources include deployment, StatefulSet, CronJob, DaemonSet, Job, and pod.
    -   Network resources include service and Ingress.
    -   Storage resources include ConfigMap, secret, and Persistent Volume Claim.
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/155719462037915_en-US.png)

    **Note:** 

    -   By default, this report displays statistics for one week. You can customize a statistics time range. In addition, you can filter events by specifying one or both of the following factors: a namespace or a sub-account ID.
    -   If you want to view the detailed operation events of a resource, we recommend that you use Resource Operation Detail.
-   Resource Operation Detail

    This report displays detailed operation information of a Kubernetes cluster resource. You must select or enter a resource type to view detailed operation information in time. This report displays the total number of operation events, namespace distribution, success rate, timing trend, and specific operation charts.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/155719462137918_en-US.png)

    **Note:** 

    -   If you want to view a CRD resource registered in Kubernetes or any other resources not listed in the report, you can enter the plural form of the target resource. For example, to view a CRD resource named AliyunLogConfig, you can enter AliyunLogConfigs.
    -   By default, this report displays statistics for one week. You can customize a statistics time range. In addition, you can filter events by specifying one or multiple factors, such as a namespace, a sub-account ID, and a status code.

logs, you can log on to Log Service to view detailed log records.

1.  Log on to the [Log Service console](https://partners-intl.console.aliyun.com/#/sls).
2.  In the left-side navigation pane, click **Project Management**, select the Project configured when you create the cluster, and then click the Project name.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/155719462112111_en-US.png)

3.  On the Logstores page, find the Logstore named audit-$\{clusterid\} and click **Search** at the right side of the Logstore. The audit logs of the cluster are stored in this Logstore.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/155719462112114_en-US.png)

    **Note:** 

    -   When you create a Kubernetes cluster, your specified log Project automatically creates a Logstore named audit-$\{clusterid\}.
    -   The audit log Logstore index is set by default. We recommend that you do not modify the index. Otherwise, the audit log reports become invalid.

To searchfor an audit log, you can use one of the following methods:

-   To querya sub-account operation record, enter the sub-account ID and then click**Search & Analysis**.
-   To query operations on a resource, enter the resource name and click**Search & Analysis**.
-   To filter out operations performed by system components, enter`NOT user.username: node NOT user.username: serviceaccount NOT user.username: apiserver NOT user.username: kube-scheduler NOT user.username: kube-controller-manager`, and then click**Search & Analysis**.

For more information, see [Log Service search and analysis methods](../../../../reseller.en-US/User Guide/Index and query/Overview.md#).

## Set resource alarms {#section_av1_lly_ngb .section}

You can use the alarm function of Log Service to set resource alarms. Alarm notifications can be sent through a DingTalk group robot, a customized Webhook, and the Message Center.

**Note:** Audit log reports provide multiple query statements. On the Logstores page, click**Dashboard**in the left-side navigation pane and then click a dashboard \(namely, an audit log report\) to display all charts, Click the menu in the upper-right corner of a chart, and then click**View Details**.

Example 1: Set an alarm notification for running a command on a container

To prevent Kubernetes cluster users from logging on to any container to run a command, you must set an alarm notification for running a command on any container. Furthermore, the alarm notification must include detailed information such as the container to which the user logged on, commands, user name, the event ID, the operation time, and the user IP address.

-   The query statement is as follows:

    ```
    
    verb : create and objectRef.subresource:exec and stage:  ResponseStarted | SELECT auditID as "event ID", date_format(from_unixtime(__time__), '%Y-%m-%d %T' ) as "operation time",  regexp_extract("requestURI", '([^\?]*)/exec\?.*', 1)as "resource",  regexp_extract("requestURI", '\?(.*)', 1)as "command" ,"responseStatus.code" as "status code",
     CASE 
     WHEN "user.username" != 'kubernetes-admin' then "user.username"
     WHEN "user.username" = 'kubernetes-admin' and regexp_like("annotations.authorization.k8s.io/reason", 'RoleBinding') then regexp_extract("annotations.authorization.k8s.io/reason", ' to User "(\w+)"', 1)
     ELSE 'kubernetes-admin' END  
     as "operation account", 
    CASE WHEN json_array_length(sourceIPs) = 1 then json_format(json_array_get(sourceIPs, 0)) ELSE  sourceIPs END
    as "source IP address" limit 100
    ```

-   The condition expression is`operation event =~ ".*"`.

Example 2: Set an alarm notification for failed Internet access to apiserver

To prevent malicious attacks on a Kubernetes cluster for which Internet access is enabled, you need to monitor the number of Internet access times and the failed access rate. Specifically, an alarm notification must be sent, when the number of Internet access times reaches a specified threshold and the failed access rate exceeds a specified threshold. Furthermore, the alarm notification must include detailed information such asto which the user IP address belongs, the user IP address, and the high risk IP address. For example, to receive an alarm notification when the number of Internet access times reaches 10 and the failed access rate exceeds 50%, configure the following settings:

-   Query statement.

    ```
    
    * | select ip as "source IP address", total as "number of access times", round(rate * 100, 2) as "failed access rate%", failCount as "number of illegal access times", CASE when security_check_ip(ip) = 1 then 'yes' else 'no' end  as "high risk IP address",  ip_to_country(ip) as "country", ip_to_province(ip) as "province", ip_to_city(ip) as "city", ip_to_provider(ip) as "network operator" from (select CASE WHEN json_array_length(sourceIPs) = 1 then json_format(json_array_get(sourceIPs, 0)) ELSE  sourceIPs END
    as ip, count(1) as total,
    sum(CASE WHEN "responseStatus.code" < 400 then 0 
    ELSE 1 END) * 1.0 / count(1) as rate,
    count_if("responseStatus.code" = 403) as failCount
    from log  group by ip limit 10000) where ip_to_domain(ip) != 'intranet'  having "number of access times" > 10 and "failed access rate%" > 50 ORDER by "number of access times" desc limit 100
    ```

-   Condition expression is`source IP address =~ ".*"`.

## Manually enable audit log report functions {#section_rgl_hvy_ngb .section}

You can manually enable audit log report functions.

1.  Enable API server audit log.

    View the API server pod settings of the three Master nodes. That is, check whether audit log settings are configured for the startup parameters, the policy file, the environment variable, and the mounting directory.

    -   Startup parameters

        ```
        containers:
          - command:
            - kube-apiserver
            - --audit-log-maxbackup=10
            - --audit-log-maxsize=100
            - --audit-log-path=/var/log/kubernetes/kubernetes.audit
            - --audit-log-maxage=7
            - --audit-policy-file=/etc/kubernetes/audit-policy.yml
        ```

    -   Policy file \( stored in the /etc/kubernetes/audit-policy.yml directory\)

        For more information, see [Configure a policy file](#).

        **Note:** If the /etc/kubernetes/ directory does not have any policy file, you need to run the vi audit-policy.yml command to create a file, and then copy the content of the policy file and paste the content to the created file.

    -   Environment variable

        ``` {#codeblock_sj6_gcn_c0d}
        env:
            - name: aliyun_logs_audit-c12ba20***************9f0b
              value: /var/log/kubernetes/kubernetes.audit
            - name: aliyun_logs_audit-c12ba20***************9f0b_tags
              value: audit=apiserver
            - name: aliyun_logs_audit-c12ba20***************9f0b_product
              value: k8s-audit
            - name: aliyun_logs_audit-c12ba20***************9f0b_jsonfile
              value: "true"
        ```

    -   Mounting directory

        ``` {#codeblock_d2u_uiw_5sg}
        volumeMounts:
            - mountPath: /var/log/kubernetes
              name: k8s-audit
            - mountPath: /etc/kubernetes/audit-policy.yml
              name: audit-policy
              readOnly: true
        volumes:
            - hostPath:
              path: /var/log/kubernetes
              type: DirectoryOrCreate
              name: k8s-audit
            - hostPath:
              path: /etc/kubernetes/audit-policy.yml
              type: FileOrCreate
              name: audit-policy
        ```

    Backup the original YAML file and then restart the API server by using a new kube-apiserver.yaml YAML file. This action will overwrite the original YAML file stored in the /etc/kubernetes/manifests/kube-apiserver.yaml directory.

    If the API server pod settings does not contain the preceding settings, you must upgrade the Kubernete cluster to the latest version. For more information, see [Upgrade a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Upgrade a Kubernetes cluster.md#).

2.  Use the latest version of the Log Service component.
    -   For how to install the Log Service component, see [Manually install the Log Service component](reseller.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#section_shf_y5r_gfb).
    -   If you have installed the Log Service component, but the audit log function is disabled, you must upgrade the component to the latest version and you must ensure that your Logtail version is not earlier than v0.16.16 and can run on Master nodes. For more information, see [Upgrade the Log Service component](reseller.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#section_b3f_y5r_gfb).
3.  Update audit log parsing methods.
    1.  Log on to the [Log Service console](https://partners-intl.console.aliyun.com/#/sls).
    2.  In the left-side navigation pane, click **Project Management**, and then click the name of the Project specified when creating your Kubernetes cluster.
    3.  The Logstores page is displayed by default. Click **Manage** on the right of the Logstore named audit-$\{clustered\}, and then click the configuration name. On the Specify Collection Mode tab page, select the **JSON Mode**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/21467/155719462137960_en-US.png)


## Use a thirty-party log solution {#section_nf2_lsf_2fb .section}

Log on to the Master node of the cluster, and you can find the source file of the audit logs in the path of /var/log/kubernetes/kubernetes.audit. The source file is in standard JSON format. When deploying a cluster, you can use other log solutions to collect and search audit logs, instead of using Alibaba Cloud Log Service.


---
keyword: [CoreDNS, logging, DNS service, monitoring]
---

# Monitor CoreDNS and analyze the CoreDNS log

CoreDNS is deployed in Container Service for Kubernetes \(ACK\) clusters and serves as a DNS server. You can check the log of CoreDNS to locate the cause of slow DNS resolution or analyze DNS queries for high-risk domain names. This topic describes how to enable the logging and monitoring of CoreDNS.

-   The logtail-ds component is installed in the cluster.

    When you create an ACK cluster, the system automatically selects the logtail-ds component. If the logtail-ds component is not installed in the cluster, you can manually install it. For more information, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

-   Make sure that the version of alibaba-log-controller is 0.2.0.0-76648ee-aliyun or later.

    ![logcontroller](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2115400361/p309799.png)

    If an earlier version of alibaba-log-controller is used, you can upgrade the logtail-ds component. For more information about how to upgrade a component, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md).


## Step 1: Enable logging for CoreDNS

**Note:** After you enable logging for CoreDNS, the CPU usage increases by about 10% and the data transfer also increases. If the replicated pods of CoreDNS are running with high CPU usage, you can scale out CoreDNS. For more information about how to scale out CoreDNS, see [Manually scale the number of pods for an application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Manage pods.md).

ACK creates a ConfigMap named **coredns** in the kube-system namespace of the cluster. You can modify the **coredns** ConfigMap by specifying the logging component in the log field of the Corefile configuration. This enables logging for CoreDNS. For more information about how to modify a ConfigMap, see [t15755.dita\#task\_1797447/section\_2px\_yjo\_j61](t15755.dita#task_1797447/section_2px_yjo_j61).

The following content is an example of the **coredns** ConfigMap that uses the default log format:

```
  Corefile: |
    .:53 {
        errors
        log // Specify the logging component. 
        health {
           lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
          ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf
        cache 30
        loop
        reload
        loadbalance
    }
    // If you want to log DNS queries of containers in other domains, you must specify the logging component for these domains by using the same configuration format. 
    demo.com:53 {
        ... 
        log // Specify the logging component. 
    }
```

## Step 2: Configure logging for CoreDNS

Logging configurations can be implemented by using CustomResourceDefinitions \(CRDs\). You can create custom resource objects of the AliyunLogConfig type. alibaba-log-controller automatically configures Log Service settings and creates log reports based on the created custom resource objects. For more information about how to create a CRD to define the AliyunLogConfig custom resource object, see [Manage custom resources](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Manage custom resources.md).

The following YAML template is a sample AliyunLogConfig configuration:

```
apiVersion: log.alibabacloud.com/v1alpha1
kind: AliyunLogConfig
metadata:
  #     Your config name, must be unique in you k8s cluster.
  name: k8s-coredns-log
  namespace: kube-system
spec:
  # logstore name to upload log
  logstore: coredns-log
  # logtail config detail
  productCode: k8s-coredns
  logtailConfig:
    inputType: plugin
    # logtail config name, should be same with [metadata.name]
    configName: k8s-coredns-log
    inputDetail:
      plugin:
        inputs:
        - type: service_docker_stdout
          detail:
            IncludeLabel:
              io.kubernetes.container.name: coredns
            Stderr: true
            Stdout: true
        processors:
        - type: processor_regex
          detail:
            KeepSource: false
            KeepSourceIfParseError: true
            Keys:
            - level
            - remote
            - port
            - id
            - type
            - class
            - name
            - proto
            - size
            - do
            - bufsize
            - rcode
            - rflags
            - rsize
            - duration
            NoKeyError: true
            NoMatchError: false
            FullMatch: false
            Regex: \[([^]]+)]\s([^:]+):(\S+)\s+-\s+(\S+)\s+"(\S+)\s+(\S+)\s+(\S+)\s+(\S+)\s+(\S+)\s+(\S+)\s+([^"]+)"\s+(\S+)\s+(\S+)\s+(\S+)\s+([\d\.]+).*
            SourceKey: content
        - type: processor_regex
          detail:
            KeepSource: false
            KeepSourceIfParseError: true
            Keys:
            - error
            - rcode
            - name
            - type
            - errorMsg
            NoKeyError: false
            NoMatchError: false
            FullMatch: false
            Regex: \[ERROR]\s+(plugin/errors):\s+(\S)+\s+(\S+)\s+([^:]*):\s+(.*)
            SourceKey: content
```

**Note:**

-   Make sure that the version of alibaba-log-controller is 0.2.0.0-76648ee-aliyun or later. If the CRD that defines the AliyunLogConfig type object is already created, delete and recreate it before you upgrade alibaba-log-controller.
-   The preceding configurations take effect only for the default log format of CoreDNS. If you use a custom log format for CoreDNS, modify the regular expression in the Regex field.

    For more information about how to customize the log format of CoreDNS, see [log](https://coredns.io/plugins/log/).

    For more information about the logging and logging configurations, see [Use CRDs to collect Kubernetes container logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use CRDs to collect Kubernetes container logs in DaemonSet mode.md).


## Step 3: Check the CoreDNS log

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the Cluster Information page, click the **Cluster Resources** tab and click the hyperlink to the right side of **Log Service Project**.

5.  In the left-side navigation page of the Logstores page, click ![resource](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7084862261/p267638.png) and click **Dashboard**. In the Dashboard list, find and click **Kubernetes CoreDNS Log Analysis**.

    On the **Kubernetes CoreDNS Log Analysis** page, you can view aggregated information about the number of queries to CoreDNS, the success rate of DNS queries, and the response latencies. You can also view the list of most frequently accessed domain names, the list of invalid domain names, the list of slow resolutions, and the list of queries for high-risk domain names.


## Step 4: Configure alert rules based on the CoreDNS log

On the **Kubernetes CoreDNS Log Analysis** page, you can configure alert rules based on each chart or list.

Find the chart or list that you want to use and choose **![more](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/9019141261/p267496.png)** \> **Save as Alert \(New Version\)** in the upper-right corner. For more information about how to configure an alert rule, see [Create an alert monitoring rule for logs](/intl.en-US/Alerting/Alerting (New)/Alert monitoring/Create an alert monitoring rule for logs.md).

![alert](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2115400361/p267588.png)

After an alert rule is created, you can view, modify, and disable the alert rule. For more information, see [Manage an alert monitoring rule](/intl.en-US/Alerting/Alerting (New)/Alert monitoring/Manage an alert monitoring rule.md).

**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Overview.md)

[Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md)


---
keyword: [CoreDNS, loging, DNS service, monitoring]
---

# Monitor and analyze the log of CoreDNS

CoreDNS is deployed in Container Service for Kubernetes \(ACK\) clusters and serves as a DNS server. You can check the log of CoreDNS to analyze the reasons why the DNS resolution is slow or resolution queries for high-risk domain names are received. This topic describes how to enable the logging and monitoring of CoreDNS.

-   The logging component alibaba-log-controller is installed.

    By default, alibaba-log-controller is installed when a cluster is created. If alibaba-log-controller is not installed, you can manually install the component. For more information, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

-   Make sure that the version of alibaba-log-controller is 0.2.0.0-76648ee-aliyun or later. For more information about how to upgrade a component, see [Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Upgrade cluster/Manage system components.md).

## Step 1: Enable the logging of CoreDNS

**Note:** After you enable the logging of CoreDNS, the CPU usage increases by about 10% and the data transfer also increases. If the pod replicas of CoreDNS are running with high CPU usage, we recommend that you scale out CoreDNS. For more information about how to scale out CoreDNS, see [Manually scale a containerized application](/intl.en-US/User Guide for Kubernetes Clusters/Application management/Workloads/Manage pods.md).

ACK creates a ConfigMap named **coredns** in the kube-system namespace of a cluster. You can modify the **coredns** ConfigMap by specifying the logging component in the log field of the Corefile configuration. This enables the logging of CoreDNS. For more information about how to modify a ConfigMap, see [t15755.dita\#task\_1797447/section\_2px\_yjo\_j61](t15755.dita#task_1797447/section_2px_yjo_j61).

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
    // If you want to log resolution queries of containers in other domains, you must specify the logging component for these domains by using the same configuration format. 
    demo.com:53 {
        ... 
        log // Specify the logging component. 
    }
```

## Step 2: Configure the logging of CoreDNS

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
-   The preceding configurations take effect for only the default log format of CoreDNS. If you use a custom log format for CoreDNS, modify the regular expression in the Regex field.

    For more information about how to customize the log format of CoreDNS, see [log](https://coredns.io/plugins/log/).

    For more information about the logging and logging configurations, see [Use CRDs to collect Kubernetes container logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use CRDs to collect Kubernetes container logs in DaemonSet mode.md).


## Step 3: Check the log of CoreDNS

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  On the details page of the cluster, click the **Cluster Resources** tab and click the hyperlink on the right side of **Log Service Project**.

5.  In the left-side navigation page of the Logstores page, click ![resource](../images/p267638.png) and click **Dashboard**. In the Dashboard list, find and click **Kubernetes CoreDNS Log Analysis**

    On the **Kubernetes CoreDNS Log Analysis** page, you can view aggregated information about the number of requests to CoreDNS, the success rate of resolution requests, and the request latencies. You can also view the list of most frequently accessed domain names, the list of invalid domain names, the list of slow resolutions, and the list of access to high-risk domain names.


## Step 4: Configure alerts based on the log of CoreDNS

On the **Kubernetes CoreDNS Log Analysis** page, you can configure alerts based on each chart or list.

1.  Find the chart or list that you want to use and choose **![more](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9019141261/p267496.png)** \> **Save as Alert** in the upper-right corner. For more information about how to configure an alert, see [Create an alert rule](/intl.en-US/Query and visualization/Alarm/Create an alert rule.md).

    After an alert rule is created, you can manage the alert rule and view the alerts that are triggered based on the alert rule. For more information, see [Manage an alert rule](/intl.en-US/Query and visualization/Alarm/Manage an alert rule.md) and [View alerts](/intl.en-US/Query and visualization/Alarm/View the evaluation results of alert rules.md).


**Related topics**  


[Overview](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Overview.md)

[Introduction and configuration of the DNS service in ACK clusters](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service discovery DNS/Introduction and configuration of the DNS service in ACK clusters.md)


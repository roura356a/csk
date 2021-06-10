---
keyword: [Ingress, Log Service, dashboards, monitor]
---

# Monitor nginx-ingress and analyze the access log of nginx-ingress

Container Service for Kubernetes \(ACK\) allows you to configure the nginx-ingress component for an ACK cluster. This component provides URLs that can be visited by servers outside the cluster, and supports server load balancing, SSL termination, and name-based virtual hosting. You can also use nginx-ingress to write the log data of HTTP requests to the stdout file. You can enable Log Service for an ACK cluster when you create the cluster. After Log Service is enabled, you can monitor nginx-ingress in real time and view dashboards in Log Service. The dashboards show statistics that are collected from the access log of nginx-ingress.

1.  The alibaba-log-controller component is installed.

    By default, alibaba-log-controller is installed when you create a cluster. If this component is not installed, you can manually install it. For more information, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).

2.  The alibaba-log-controller component is upgraded.

    Run the `kubectl edit deployment alibaba-log-controller -n kube-system` command to upgrade the component.


## Precautions

Take note of the following limits when you deploy the custom resource definition \(CRD\) for AliyunLogConfig:

-   Make sure that the version of alibaba-log-controller is `0.2.0.0-76648ee-aliyun` or later. After you upgrade alibaba-log-controller, if you find that the CRD is already deployed, delete the CRD and redeploy it.
-   The CRD for AliyunLogConfig applies only to the default log format that ACK defines for the Ingress controller. If you have changed the log format, you must modify the `processor_regex` settings in the nginx-ingress.yaml file. For more information, see [Use CRDs to collect Kubernetes container logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use CRDs to collect Kubernetes container logs in DaemonSet mode.md).
-   The `IncludeLabel` label specifies the label information retrieved by using the docker inspect command.
-   A namespace and a container name in a Kubernetes cluster are separately mapped to the following Docker labels: `io.kubernetes.pod.namespace` and `io.kubernetes.container.name`. For example, the pod that you create belongs to the backend-prod namespace and the container name is worker-server.
    -   If you set the `io.kubernetes.pod.namespace : backend-prod` whitelist, log files of all containers in the specified pod are collected.
    -   If you set the `io.kubernetes.container.name : worker-server` whitelist, log files of the specified container are collected.
-   In a Kubernetes cluster, we recommend that you specify only the `io.kubernetes.pod.namespace` and `io.kubernetes.container.name` labels. In other cases, you can specify the IncludeEnv or ExcludeEnv label. For more information, see [Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use the console to collect Kubernetes stdout and stderr logs in DaemonSet mode.md).

## Create a CRD to collect the log files of nginx-ingress

You can create CRDs to configure log collection in ACK clusters. You can deploy a CRD for AliyunLogConfig. alibaba-log-controller automatically generates configurations for Log Service to collect log data and update data in relevant dashboards.

Run the following command to deploy the CRD for AliyunLogConfig:

```
cat <<-EOF | kubectl apply -n kube-system -f -
apiVersion: log.alibabacloud.com/v1alpha1
kind: AliyunLogConfig
metadata:
  # your config name, must be unique in you k8s cluster
  name: k8s-nginx-ingress
spec:
  # logstore name to upload log
  logstore: nginx-ingress
  # product code, only for k8s nginx ingress
  productCode: k8s-nginx-ingress
  # logtail config detail
  logtailConfig:
    inputType: plugin
    # logtail config name, should be same with [metadata.name]
    configName: k8s-nginx-ingress
    inputDetail:
      plugin:
        inputs:
        - type: service_docker_stdout
          detail:
            IncludeLabel:
              io.kubernetes.container.name: nginx-ingress-controller  #If multiple Ingress controllers are configured, duplicate log files may be collected. Read the description of IncludeLabel in the Precautions section.
            Stderr: false
            Stdout: true
        processors:
        - type: processor_regex
          detail:
            KeepSource: false
            Keys:
            - client_ip
            - x_forward_for
            - remote_user
            - time
            - method
            - url
            - version
            - status
            - body_bytes_sent
            - http_referer
            - http_user_agent
            - request_length
            - request_time
            - proxy_upstream_name
            - upstream_addr
            - upstream_response_length
            - upstream_response_time
            - upstream_status
            - req_id
            - host
            - proxy_alternative_upstream_name
            NoKeyError: true
            NoMatchError: true
            Regex: ^(\S+)\s-\s\[([^]]+)]\s-\s(\S+)\s\[(\S+)\s\S+\s"(\w+)\s(\S+)\s([^"]+)"\s(\d+)\s(\d+)\s"([^"]*)"\s"([^"]*)"\s(\S+)\s(\S+)+\s\[([^]]*)]\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s*(\S*)\s*\[*([^]]*)\]*.*
            SourceKey: content
EOF
```

## View the log data of nginx-ingress and dashboards

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the Projects section, click the name of the project that you specified when you created the ACK cluster. The details page of the project appears. By default, a project that is named in the format of **k8s-log-\{cluster-id\}** is created for the ACK cluster.

3.  In the left-side navigation pane, choose **nginx-ingress** \> **Visual Dashboards** to view the dashboards of nginx-ingress.


## Ingress overview

The Ingress overview dashboard displays information about network traffic that flows through nginx-ingress for a website. You can view the following information:

-   Website data of the last 24 hours, including the number of page views \(PVs\), the number of unique visitors \(UVs\), inbound and outbound traffic, the average latency, the proportion of mobile users, and the proportions of 5xx errors and 404 errors.
-   Website data of the last 1 minute, including the number of PVs, the number of UVs, the success rate of requests, the proportion of 5xx errors, the average latency, the P95 latency, and the P99 latency.
-   Detailed information about requests within the last 24 hours, including the PV trend \(compared with the PV trend within the last 24 hours and the last 7 days\), regional distribution of request sources, the top N source areas and cities, the proportion of mobile users, and the proportions of Android users and iOS users.
-   Top N URLs within the last 1 hour, including the 10 URLs of the highest PVs, the 10 URLs of the highest latencies, the 10 URLs that return the most 5xx errors, and the 10 URLs that return the most 404 errors.

![Ingress overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40696.png)

## Ingress access center

The Ingress access center dashboard displays up-to-date information about user requests. You can obtain and analyze the following data to help make business decisions: the numbers of UVs and PVs within the last 24 hours, the regional distribution of PVs and UVs, top N areas by request, top N cities by request, top N browsers of the highest PVs, top N source IP addresses of the highest PVs, the proportion of mobile users, and the proportions of Android users and iOS users.

![Ingress access center](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40704.png)

## Ingress monitoring center

The Ingress monitoring center dashboard provides real-time monitoring and alerting data of the website. You can view the following data within the last 1 hour: the success rate of requests, the proportions of 5xx errors, 404 errors, and requests that are not forwarded, the average latency, the P95, P99, and P999 latencies, the request distribution by status code, the proportion of PVs on each Ingress, top 10 Services of the highest PVs, top 10 Services of the highest request failure rates, top 10 Services of the highest average latencies, and top 10 Services of the highest data transfer.

![Ingress monitoring center](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40705.png)

## Ingress monitoring center for blue/green deployment

The Ingress monitoring center for blue/green deployment dashboard displays the real-time status of a Service version release and compares the specified Service versions. This allows you to identify exceptions and roll back the Service at the earliest opportunity. You must specify **ServiceA** and **ServiceB** for monitoring and comparison. The dashboard displays the following dynamic monitoring data of each Service: the number of PVs, the proportion of 5xx errors, the success rate of requests, the average latency, the P95, P99, and P999 latencies, and the amount of data transfer.

![Ingress monitoring center for blue/green deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40706.png)

## Ingress exceptions center

The Ingress exceptions center automatically detects anomalies in the log data of nginx-ingress. This service uses the machine learning algorithms provided by Log Service and the time series analysis algorithms.

![Ingress exceptions center](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3445359951/p40707.png)

## Configure alerting

Log Service enables interactive log analytics and provides visualized dashboards. You can also configure alerting based on the dashboards. You can select one or more alerting methods, such as Email, WebHook-DingTalk Bot, WebHook-Custom, or Notifications.

For more information about how to configure alerting, see [Create an alert rule](/intl.en-US/Alerting/Alerting (Old)/Create an alert rule.md).

The following example describes how to configure an alert rule for the 5xx Proportion chart. The system checks whether the alert conditions are met every five minutes. If the proportion of 5xx errors within the specified time range reaches 1%, an alert is triggered.

1.  In the Dashboard section, click Ingress Monitoring Center. On the page that appears, move the pointer over the ![Hint](../images/p229419.png) icon in the upper-right corner of the **5XX Proportion** chart, and click **Create Alert**.

    ![Configure an alert](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3445359951/p40708.png)

2.  On the **Alert Configuration** wizard page, set **Alert Name**, **Search Period**, **Frequency**, and **Trigger Condition**. `total` specified in the Query field is the proportion of 5XX. Set Trigger Condition to `total > 1`.

    ![Configure an alert rule](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3445359951/p40709.png)

3.  On the **Notifications** wizard page, select one or more alerting methods based on your requirements and set the parameters. Then, click **Submit** to create the alert rule.


## Subscribe to a dashboard

Log Service allows you to subscribe to dashboards. This feature takes snapshots of a dashboard and sends the snapshots by email or DingTalk group message at a specified interval.

For more information about how to subscribe to a dashboard, see [Subscribe to a dashboard](/intl.en-US/Visualization/Dashboard/Subscribe to a dashboard.md).

The following example describes how to subscribe to the Ingress overview dashboard. After you subscribe to the dashboard, a message is sent at 10:00 every morning to the specified DingTalk group.

1.  In the Dashboard section, click Ingress Overview V1.2. On the Ingress Overview V1.2 tab, choose **Subscribe** \> **Create**.

2.  On the Subscription Configuration wizard page, select Daily and 10:00 in the Frequency section. Disable **Add Watermark** and click **Next**.

3.  On the Notifications wizard page, set Notifications to **WebHook-DingTalk Bot** and set Request URL to the webhook URL of the DingTalk chatbot. Then, click **Submit** to create the subscription.



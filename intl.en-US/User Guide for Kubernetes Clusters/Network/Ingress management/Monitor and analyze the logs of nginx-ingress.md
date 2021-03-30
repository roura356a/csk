---
keyword: [ack-ingress-nginx, Log Service, analyze, monitor]
---

# Monitor and analyze the logs of nginx-ingress

Container Service for Kubernetes \(ACK\) allows you to configure the nginx-ingress component for an ACK cluster. This component provides URLs that can be visited by servers outside the cluster, server load balancing, SSL termination, and name-based virtual hosting. You can also use nginx-ingress to write the logs of HTTP requests to the stdout file. ACK is also integrated with Log Service. This allows you to create dashboards to monitor and analyze the nginx-ingress logs with simple steps.

1.  The alibaba-log-controller component is installed.

    By default, alibaba-log-controller is installed when you create a cluster. If this component is not installed, you can manually install it. For more information, see [Use Log Service to collect container logs](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Use Log Service to collect container logs.md).

2.  The alibaba-log-controller component is updated.

    You can use one of the following methods to update alibaba-log-controller:

    -   Run the `kubectl edit deployment alibaba-log-controller -n kube-system` command.
    -   Update alibaba-log-controller in the ACK console.
        1.  Log on to the [ACK console](https://cs.console.aliyun.com).
        2.  In the left-side navigation pane, click **Clusters**.
        3.  On the **Clusters** page, click the name of the cluster that you want to manage, or click **Details** in the **Actions** column.
        4.  In the left-side navigation pane, click **Workloads**.
        5.  On the **Deployments** tab, select the namespace **kube-system** of the cluster, click Edit in the Actions column of **alibaba-log-controller**, modify the configurations, and then click Update.

**Note:**

To update alibaba-log-controller in the kube-system namespace of the cluster that you want to manage, set the following parameters based on your needs:

-   Image Name: Set the image name in the format of registry-vpc.\{region-id\}.aliyuncs.com/acs/log-controller. Replace `{region-id}` with the ID of the region where the cluster is deployed. For example, cn-hangzhou, cn-beijing, or ap-southeast-1 can be specified.
-   Image Version: Use version 0.2.0.0-7f4d3bb-aliyun or later.

## Create a custom resource to collect the logs of nginx-ingress

You can create a custom resource for alibaba-log-controller to collect the logs of nginx-ingress. You can use one of the following methods to create a custom resource:

-   Run the kubectl command to write the following content to the nginx-ingress.yaml file.

    ```
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
                  io.kubernetes.container.name: nginx-ingress-controller
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
                Regex: ^(\S+)\s-\s\[([^]]+)]\s-\s(\S+)\s\[(\S+)\s\S+\s"(\w+)\s(\S+)\s([^"]+)"\s(\d+)\s(\d+)\s"([^"]*)"\s"([^"]*)"\s(\S+)\s(\S+)+\s\[([^]]*)]\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s*(\S*)\s*\[*([^]]*)\]*. *
                SourceKey: content
    ```

    Run the kubectl apply -f nginx-ingress.yaml command to create the nginx-ingress.yaml custom resource.

-   Use an orchestration template to create a custom resource.
    1.  Log on to the [ACK console](https://cs.console.aliyun.com).
    2.  Write the nginx-ingress.yaml file to an orchestration template.

        For more information about how to create an orchestration template, see [Create an orchestration template](/intl.en-US/User Guide for Kubernetes Clusters/Application marketplace/Template management/Create an orchestration template.md).

    3.  Use the orchestration template to create an application. Select the default **namespace** of the cluster when you create the application.

**Note:**

-   Make sure that the version of alibaba-log-controller is `0.2.0.0-76648ee-aliyun` or later. After you update alibaba-log-controller, delete the custom resource and recreate it.
-   The custom resource that you create applies to only nginx-ingress. If the Ingress controller of your cluster is not nginx-ingress, modify the `processor_regex` field based on your needs in the nginx-ingress.yaml file. For more information, see [Use CRDs to collect Kubernetes container logs in the DaemonSet mode](/intl.en-US/Data Collection/Logtail collection/Container log collection/Use CRDs to collect Kubernetes container logs in the DaemonSet mode.md).

## View the logs of nginx-ingress on dashboards

1.  Log on to the [Log Service console](https://sls.console.aliyun.com).

2.  In the **Projects** section, click the name of the project that you specified when you created the ACK cluster. The details page of the project appears. By default, a project that is named in the format of **k8s-log-\{cluster-id\}** is created for the ACK cluster.

3.  On the details page of the project, you are redirected to the Logstores tab. All the logs of nginx-ingress are stored in the Logstore named **nginx-ingress**. To view the logs of nginx-ingress on dashboards, click the **Dashboard** icon.


## Ingress Overview

The Ingress Overview dashboard displays the data of a website that is monitored by nginx-ingress. You can view the following information:

-   Website data of the last 24 hours, including the number of page views \(PVs\), the number of unique visitors \(UVs\), inbound and outbound traffic, the average latency, the proportion of mobile users, and the proportions of 5xx errors and 404 errors.
-   Website data of the last one minute, including the number of PVs, the number of UVs, the success rate of requests, the proportion of 5xx errors, the average latency, the P95 latency, and the P99 latency.
-   Detailed information about requests in the last 24 hours, including the PV trend \(compared with the PV trend in the last seven days\), regional distribution of request sources, the top 10 source areas and cities, the proportion of mobile users, and the proportions of Android users and iOS users.
-   Top 10 URLs in the last one hour, including the 10 URLs of the highest PVs, the 10 URLs of the highest latencies, the 10 URLs that return the most 5xx errors, and the 10 URLs that return the most 404 errors.

![Ingress Overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40696.png)

## Ingress access center

The Ingress access center dashboard displays up-to-date information about access requests. You can obtain the following data for operations analysis: the numbers of UVs and PVs in the last 24 hours, the regional distribution of PVs and UVs, seven-day forecasts of PVs and UVs, top 10 source areas and cities, the browsers of the highest PVs, the source IP addresses of the highest PVs, the proportion of mobile users, and the proportions of Android users and iOS users.

![Ingress access center](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40704.png)

## Ingress monitoring center

The Ingress monitoring center dashboard provides real-time monitoring and alerting data of the website. You can view the following data of the last one hour: the success rate of requests, the proportions of 5xx errors, 404 errors, and requests that are not forwarded, the average latency, the P95, P99, and P9999 latencies, the request distribution by status code, the proportion of PVs on each Ingress, the 10 services of the highest PVs, the 10 services of the highest request failure rates, the 10 services of the highest average latencies, and the 10 services of the highest traffic.

![Ingress monitoring center](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40705.png)

## Ingress monitoring center for blue/green deployment

The Ingress monitoring center for blue/green deployment dashboard displays the real-time status of a service version release and compares the specified service versions. This allows you to identify exceptions and roll back the service at the earliest opportunity. You must set **ServiceA** and **ServiceB** for monitoring and comparison. The dashboard displays the following dynamic monitoring data of each service: the number of PVs, the proportion of 5xx errors, the success rate of requests, the average latency, the P95, P99, and P9999 latencies, and the traffic data.

![Ingress monitoring center for blue/green deployment](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/2445359951/p40706.png)

## Ingress exceptions center

The Ingress exceptions center automatically detects exceptions in the logs of nginx-ingress. This service uses the machine learning algorithms provided by Log Service and the time series analysis algorithms.

![Ingress exceptions center](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3445359951/p40707.png)

## Configure an alert

Log Service enables interactive log analysis and visualized dashboards. You can also configure alerts for a chart in a dashboard. You can select one or more notification methods, such as Email, WebHook-DingTalk Bot, WebHook-Custom, or Notifications.

For more information about how to configure an alert, see [Create an alert rule](/intl.en-US/Query and visualization/Alarm/Create an alert rule.md).

The following example describes how to configure an alert rule for the 5xx Proportion chart. The system checks whether the alert conditions are met once every five minutes. If the proportion of 5xx errors in the specified time range reaches 1%, an alert is triggered.

1.  In the Dashboard section, click Ingress Monitoring Center. On the page that appears, find the **5XX Proportion** chart. Click the More icon in the upper-right corner of the chart, and click **Create Alert**.

    ![Configure an alert](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3445359951/p40708.png)

2.  On the **Alert Configuration** wizard page, set **Alert Name**, **Search Period**, and **Search Interval**. Set **Trigger Condition** to `total > 1`, and click Next.``

    ![Configure an alert](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3445359951/p40709.png)

3.  On the **Notifications** wizard page, select one or more notification methods and set the parameters.

    ![Select notification methods](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3445359951/p40710.png)


## Subscribe to a dashboard

Log Service enables dashboard subscription. You can use this feature to take snapshots of a dashboard and send the snapshots by email or DingTalk group message at a specified interval.

For more information about how to subscribe to a dashboard, see [Subscribe to a dashboard](/intl.en-US/Query and visualization/Dashboard/Subscribe to a dashboard.md).

The following example describes how to subscribe to the Ingress Overview dashboard. After you subscribe, a message is sent at 10:00 every morning to the specified DingTalk group.

1.  In the Dashboard section, click Ingress Overview. On the Ingress Overview tab, choose **Subscribe** \> **Create**.

2.  On the Create Subscription wizard page, select Daily and 10:00 in the Frequency section. Disable **Add Watermark** and click Next.

3.  On the Notifications wizard page, set Notifications to **WebHook-DingTalk Bot** and set Request URL to the webhook URL of the DingTalk chatbot.



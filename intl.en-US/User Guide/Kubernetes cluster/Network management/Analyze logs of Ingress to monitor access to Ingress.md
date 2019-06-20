# Analyze logs of Ingress to monitor access to Ingress {#concept_nmh_nks_vdb .concept}

This topic describes how to enable Ingress to collect logs, how to analyze log reports of different types, and how to use reports by using alarm configurations and report subscription. Alibaba Cloud Container Service for Kubernetes \(ACK\) provides Ingress to collect all HTTP requests to a standard output. You can use Log Service \(integrated with ACK\) to create dashboards to analyze logs of Ingress and monitor access to Ingress.

## Before you begin {#section_chy_qqs_vdb .section}

1.  Install the log component in a Kubernetes cluster.

    **Note:** If you want to create a new Kubernetes cluster oruse an existing Kubernetes cluster in which the log component is not installed,follow these steps:

    -   For information about how to install the log component in a Kubernetes cluster when the cluster is created, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
    -   For information about how to install the log component in a Kubernetes cluster after the cluster is created, see [Use Log Service to collect Kubernetes cluster logs](reseller.en-US/User Guide/Kubernetes cluster/Log management/Use Log Service to collect Kubernetes cluster logs.md#).
2.  Upgrade the log component `alibaba-log-controller` of the target Kubernetes cluster.

    The log component `alibaba-log-controller` is a deployment application located in the kube-system namespace of the target Kubernetes cluster. To upgrade it, you must modify the following two parameters:

    -   Image name: Replace `{region-id}` in the image name registry-vpc.\{region-id\}.aliyuncs.com/log-service/alibabacloud-log-controller with the ID of the region to which the target Kubernetes cluster belongs. For example, `{region-id}` can be replaced with cn-hangzhou, cn-beijing, or ap-southeast-1.
    -   Image version: It must be the version `0.2.0.0-76648ee-aliyun` or later.
    You can choose one of the following two upgrade methods:

    -   Run the kubectl edit deployment alibaba-log-controller -n kube-system command.
    -   Update by using the Container Service console.

        To do so, follow these steps:

        1.  Log on to the Container Service console.
        2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Deployments**.
        3.  Select the target Kubernetes cluster and the **kube-system** namespace, find **alibaba-log-controller**, and then, in the **Action** column, click **Edit**.

## Deploy the configurations for Ingress to collect logs {#section_g4b_sqs_vdb .section}

**Overview**

The following are the configurations for Ingress to collect logs. The configurations can be viewed as the expanded Kubernetes Custom Resource Definitions \(CRDs\). Therefore, the configurations are referred to as only CRD configurations later in this topic. When the CRD configurations are deployed, the log component automatically creates the parameters and report resources that are related to Log Service.

``` {#codeblock_tlf_1a7_vnv}
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
            NoKeyError: true
            NoMatchError: true
            Regex: ^(\S+)\s-\s\[([^]]+)]\s-\s(\S+)\s\[(\S+)\s\S+\s"(\w+)\s(\S+)\s([^"]+)"\s(\d+)\s(\d+)\s"([^"]*)"\s"([^"]*)"\s(\S+)\s(\S+)+\s\[([^]]*)]\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s(\S+)\s*(\S*).*
            SourceKey: content
```

-   If you have deployed the CRD configurations before you upgrade the log component alibaba-log-controller to version `0.2.0.0-76648ee-aliyun`, you must first delete the deployed CRD configurations, and then redeploy the CRD configurations after the upgrade.
-   The preceding CRD configurations take effect only for the log format of the default Ingress Controller of ACK. If the log format is modified, you must modify the regular expression \(the `processor_regex` part\) in the CRD configurations according to the procedures described in[Configure Kubernetes log collection on CRD](../../../../reseller.en-US/User Guide/Logtail collection/Container log collection/Configure Kubernetes log collection on CRD.md#).

**Procedure**

You can use one of the following two methods to deploy the CRD configurations:

-   Use a kubectl command.

    Save the preceding CRD configurations as an nginx-ingress.yaml file, and then run the kubectl apply -f command.

-   Use an orchestration template.
    1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
    2.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    3.  Save the preceding CRD configurations as an orchestration template. For more information, see [Create an orchestration template](reseller.en-US/User Guide/Kubernetes cluster/Template management/Create an orchestration template.md#).
    4.  In the default namespace of the target Kubernetes cluster, use this template to create an application.

## View logs and reports of Ingress {#section_msy_bhq_bhb .section}

1.  Log on to the [Log Service console](https://partners-intl.console.aliyun.com/#/sls)
2.  Click the Project associated with the target Kubernetes cluster.

    **Note:** The default name of the Project is **k8s-log-\{cluster-id\}**.

    Then, the **nginx-ingress** Logstore is display. All the logs of Ingress are store in this Logstore.

3.  In the left-side navigation pane, click **Dashboard** to view all the reports of Ingress.

    **Note:** The following are the reports of Ingress logs: Ingress Overview , Ingress Access Center, Ingress Monitoring Center, Ingress Monitoring Center for Blue/Green Deployment, and Ingress Exceptions Center.


## Ingress Overview report {#section_rnq_vnq_bhb .section}

An Ingress Overview report displays the overall status of the Ingress with the following information:

-   Overall status \(each day\): PV, UV, traffic, request latency, and the ratio of the number of mobile terminal users to the number of all users.
-   Website status in real time \(each minute\): PV, UV, rate of successful access, the proportion of 5XX errors in all errors, average latency, P95 latency, and P99 latency.
-   Statistics of users requests \(each day\): PVs of today and seven days, visit distribution by area, the top 10 province and cities by requests, shares of mobile terminals, and shares of Android and IOS terminals.
-   Statistics of Top URLs \(each hour\): Top 10 URLs by request, Top10 URLs by latency, Top 10 5XX URLs and Top 10 404 URLs.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156102736740696_en-US.png)

## Ingress Access Center {#section_e2b_1pq_bhb .section}

Ingress Access Center provides the statistics of access requests that can be used to analyze the operation status. This report contains the following information:

-   UV and PV of the current day
-   UV and PV distribution
-   UV and PV trend
-   Top 10 areas and cities by request
-   Top browsers
-   Top IP addresses of accesses
-   Shares of mobile terminals
-   Shares of Android and IOS

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156102736740704_en-US.png)

## Ingress Monitoring Center {#section_bl3_2pq_bhb .section}

Ingress Monitoring Center provides the real-time monitoring statistics for a website. This report contains the following statistics:

-   Rate of successful requests
-   Proposition of 404 status codes
-   Proposition of status codes of 5XX
-   Non-forwarded request proportion
-   Average latency
-   P95, P99, and P9999 latency
-   Top 10 services by requests
-   Top 10 services by failures
-   Top 10 services by latency
-   Top 10 services by traffic

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156102736740705_en-US.png)

## Ingress Monitoring Center for Blue/Green Deployment {#section_ny3_hpq_bhb .section}

This report is used to monitor version releases and compare the blue and green versions. This helps you quickly find release exceptions and then roll back the the original version. In this report, you need to select the blue and green versions \(for example, Service A and Service B\). The report dynamically displays the statistics about the two versions, including PV, the ratio of 5XX errors to all errors, rate of success, average latency, traffic, and latency of P5, P99, and P9999.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156102736840706_en-US.png)

## Ingress Exceptions Center {#section_rst_jpq_bhb .section}

Ingress Exceptions Center operates on the basis of the algorithm of machine learning provided by Log Service. It automatically detects exceptions from the Ingress metrics by using multiple timing analysis algorithms.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156102736840707_en-US.png)

## Configure an alarm {#section_ozp_mpq_bhb .section}

You can configure an alarm for each of the preceding reports. For more information, see [Configure an alarm](../../../../reseller.en-US/User Guide/Alarm/Configure an alarm/Configure an alarm.md#).

The following shows how to configure an alarm for the statistics of the porportion of 5xx status codes:

1.  Open the Ingress Monitoring Center report, move your pointer to the upper-right corner of the **5XX Proportion** chart, and then, in the displayed box, click **Create Alarm**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156102736840708_en-US.png)

2.  Set the alarm name, search interval, and enter a trigger condition `total > 1`.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156102736840709_en-US.png)

3.  Set a notification method as needed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16678/156102736940710_en-US.png)


## Subscribe to a scheduled report {#section_hn2_2rq_bhb .section}

You can schedule Log Service to render a report to a figure and then send the figure to you through an email or sent the figure to a specific DingTalk group. For more information, see [Subscribe to dashboard snapshots](https://www.alibabacloud.com/help/doc-detail/107758.htm?spm=a2c63.p38356.879954.13.139d6f67aunGCn#concept-yhp-mmg-vgb).

The following shows how to subscribe to a scheduled Ingress overview report \(the figure generated by rendering the report is sent to the specified DingTalk group at 10:00 every day\):

1.  Open the Ingress overview report. Then, in the upper-right corner, click **Subscribe**.
2.  On the displayed page, select Daily and 10:00 from the two drop-down lists of **Frequency**, turn off **Add Watermark**.
3.  Select the **WebHook-DingTalk Bot** from the **Notifications** drop-down list, and then enter the request URL.


---
keyword: [external Kubernetes cluster, multi-cluster, enable Log Service for Kubernetes clusters]
---

# Enable Log Service for an external Kubernetes cluster

This topic describes how to enable Log Service for an external Kubernetes cluster by deploying an application in the cluster. This provides a unified approach to manage Kubernetes clusters that are deployed across regions.

An external Kubernetes cluster is registered in the Container Service for Kubernetes \(ACK\) console. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Create a cluster registration proxy and register an on-premises cluster.md).

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. On the Alibaba Cloud Apps tab, find and click **ack-sls-logtail**.

    In the upper-right corner of the **App Catalog** page, you can enter **ack-sls-logtail** into the Name search bar and click the search icon. You can also enter a keyword to perform a fuzzy match.

4.  On the **App Catalog - ack-sls-logtail** page, set the parameters in the **Deploy** section.

    -   **Cluster**: Select an external cluster to deploy the application. The related resource objects are deployed in the selected cluster.
    -   **Namespace**: Select the namespace in which the related resource objects are deployed. If you do not select a namespace, the default namespace is used. Except for underlying computing resources such as nodes and persistent volumes \(PVs\), most resources objects are scoped to namespaces.
    -   **Release Name**: The release name of the related resource objects.
5.  On the **App Catalog - ack-sls-logtail** page, click the **Parameters** tab and set the parameters.

    ![Log Service parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3465359951/p76771.png)

    |Parameter|Description|
    |---------|-----------|
    |**AccessKeyId**|The AccessKey ID of your Alibaba Cloud account. Your account must be authorized to access Application Real-Time Monitoring Service \(ARMS\).|
    |**AccessKeySecret**|The AccessKey Secret of your Alibaba Cloud account.|


After the application is deployed, you can execute the following YAML file in the ACK console to check whether Log Service can work.

```
apiVersion: v1
kind: Pod
metadata:
  name: my-demo
spec:
  containers:
  - name: my-demo-app
    image: 'registry.cn-hangzhou.aliyuncs.com/log-service/docker-log-test:latest'
    env:
    ######### Specify environment variables ###########
    - name: aliyun_logs_log-stdout
      value: stdout
    - name: aliyun_logs_log-varlog
      value: /var/log/*.log
    - name: aliyun_logs_mytag1_tags
      value: tag1=v1
    ###############################
    ######### Configure volume mounting ###########
    volumeMounts:
    - name: volumn-sls-mydemo
      mountPath: /var/log
  volumes:
  - name: volumn-sls-mydemo
    emptyDir: {}
  ###############################
```

If you can find log data in the log-stdou and log-varlog Logstores under the related Log Service project, it indicates that Log Service is working normally.

**Note:** Log data is collected with a latency.

If Ingress-nginx is deployed in the cluster, see [Monitor and analyze the logs of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Monitor and analyze the logs of nginx-ingress.md).

**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Overview of registered clusters.md)


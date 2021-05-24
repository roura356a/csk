---
keyword: [external Kubernetes cluster, multi-cluster, enable Log Service for Kubernetes clusters]
---

# Enable Log Service for an external Kubernetes cluster

You can enable Log Service for external Kubernetes clusters that are registered in the Container Service for Kubernetes \(ACK\) console. This allows you to centrally manage Kubernetes clusters that are deployed across regions. This topic describes how to enable Log Service for an external cluster by deploying an application.

An external Kubernetes cluster is registered in the ACK console. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Create a cluster registration proxy and register an on-premises cluster.md).

1.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, click the **Alibaba Cloud Apps** tab. On the Alibaba Cloud Apps tab, find and click **ack-sls-logtail**.

    A great number of applications are displayed on the **Alibaba Cloud Apps** tab. You can enter **ack-sls-logtail** into the Name search box to search for the component. You can also enter a keyword to perform a fuzzy match.

4.  On the **App Catalog - ack-sls-logtail** page, set the parameters in the **Deploy** section.

    -   **Cluster**: Select the cluster where you want to deploy the application. The resource objects are deployed in the selected cluster.
    -   **Namespace**: Select the namespace to which the resource objects belong. By default, the default namespace is selected. Except for underlying computing resources such as nodes and persistent volumes \(PVs\), most resource objects are scoped to namespaces.
    -   **Release Name**: Enter the release name of the resource object.
5.  On the **App Catalog - ack-sls-logtail** page, click the **Parameters** tab and set the parameters.

    ![Log Service parameters](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3465359951/p76771.png)

    |Parameter|Description|
    |---------|-----------|
    |**AccessKeyId**|The AccessKey ID of your Alibaba Cloud account. Your account must be authorized to access Application Real-Time Monitoring Service \(ARMS\).|
    |**AccessKeySecret**|The AccessKey secret of your Alibaba Cloud account.|


After the application is deployed, you can execute the following YAML file in the ACK console to check whether Log Service is enabled.

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
    ######### Configure environment variables ###########
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

**Note:** The display of log data may have latency.

If Ingress-nginx is deployed in the cluster, see [Monitor and analyze the logs of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Monitor and analyze the logs of nginx-ingress.md).

**Related topics**  


[Overview of registered clusters](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of registered clusters/Overview of registered clusters.md)


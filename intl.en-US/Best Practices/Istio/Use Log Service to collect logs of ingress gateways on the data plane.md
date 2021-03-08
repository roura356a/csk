# Use Log Service to collect logs of ingress gateways on the data plane

Container Service for Kubernetes \(ACK\) integrates with Log Service. To use ACK to collect access logs of an ingress gateway on the data plane, you must enable Log Service for the Kubernetes cluster where the ingress gateway resides when you create the cluster. This topic shows you how to enable and configure log collection and view the collected logs.

-   At least one Alibaba Cloud Service Mesh \(ASM\) instance is created. For more information, see [Create an ASM instance]().
-   At least one ACK cluster is added to the ASM instance. For more information, see [Add a cluster to an ASM instance]().
-   At least one ingress gateway is deployed in the ACK cluster that is added to the ASM instance. For more information, see [Deploy an ingress gateway service]().

## Step 1: Install Logtail for the target Kubernetes cluster

**To create a Kubernetes cluster, follow these steps:**

1.  Log on to the [the ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Clusters** \> Clusters.

3.  On the **Clusters** page that appears, click **Create Kubernetes Cluster** in the upper-right corner. For more information, see [Create a managed Kubernetes cluster](/intl.en-US/Quick Start/Basic operations/Create a managed Kubernetes cluster.md).

4.  In the **Component Configuration** step, select the **Enable Log Service** check box to install the logging plug-in.

5.  Set the other parameters as required and click **Create Cluster** to create a cluster.


**To install Logtail in a Kubernetes cluster, follow these steps:**

1.  In the left-side navigation pane, choose **Clusters** \> Clusters.

2.  On the Clusters page that appears, select the target cluster and click **Manage** in the Actions column. The Basic Information page of the cluster appears.

3.  In the left-side navigation pane, click **Add-ons**.

4.  Find **logtail-ds** in the optional component list and click **Install** in the **Actions** column.


**To upgrade Logtail of a version earlier than v0.16.24.0-1fa7551-aliyun for a Kubernetes cluster, follow these steps:**

1.  On the management page of the target cluster, click **Add-ons**.

2.  Find **logtail-ds** in the optional component list and click **Upgrade** in the **Actions** column.


## Step 2: Configure log collection

To use Logtail to collect access logs of an ingress gateway, you must configure the log collection feature in the configuration file of the ACK cluster where the ingress gateway resides.

1.  Create a YAML file by using the following template:

    **Note:** You must prepare a configuration file for each Kubernetes cluster on the data plane.

    ```
    apiVersion: log.alibabacloud.com/v1alpha1
    kind: AliyunLogConfig
    metadata:
      # your config name, must be unique in you k8s cluster
      name: mesh-ingress-log-config
      namespace: kube-system
    spec:
      project: k8s-log-${K8SClusterId}
      # logstore name to upload log
      logstore: mesh-ingress-log
      # product code, you should not change it
      productCode: k8s-istio-ingress
      # logtail config detail
      logtailConfig:
        inputType: plugin
        configName: mesh-ingress-log-config
        inputDetail:
          plugin:
            inputs:
            - detail:
                IncludeLabel:
                  io.kubernetes.pod.name: ^istio-ingressgateway-. *$
                Stderr: false
                Stdout: true
              type: service_docker_stdout
            processors:
            - detail:
                Anchors:
                - FieldName: log
                  FieldType: json
                KeepSource: true
                NoKeyError: true
                NoMatchError: true
                SourceKey: content
              type: processor_anchor
            - type: processor_rename
              detail:
                DestKeys:
                - host
                - request_length
                - body_bytes_sent
                - request_time
                - method
                - url
                - version
                - req_id
                - status
                - proxy_upstream_name
                - upstream_addr
                - upstream_response_time
                - http_user_agent
                - x_forward_for
                SourceKeys:
                - log_authority
                - log_bytes_received
                - log_bytes_sent
                - log_duration
                - log_method
                - log_path
                - log_protocol
                - log_request_id
                - log_response_code
                - log_upstream_cluster
                - log_upstream_host
                - log_upstream_service_time
                - log_user_agent
                - log_x_forwarded_for
    ```

2.  Replace $\{K8SClusterID in the YAML template with the ID of your Kubernetes cluster.

3.  To collect logs of an ingress gateway that is not created by using the ASM console, you must change the value of io.kubernetes.pod.name in the YAML template to the following format:

    ```
    ^(^istio-ingressgateway-. *$)|(^[Name-of-your-customized-ingressgateway]-. *$)$
    ```

    For example, if the name of the ingress gateway to deploy is my-ingressgateway, change the value of io.kubernetes.pod.name in the following way:

    ```
    ^(^istio-ingressgateway-.*$)|(^my-ingressgateway-. *$)$
    ```

4.  Connect to the Kubernetes cluster. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md) or [Use SSH to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use SSH to connect to an ACK cluster.md).

5.  After you edit the YAML file, deploy the file for the Kubernetes cluster.

    ```
    kubectl apply -f [The path of the YAML file]
    ```


## Step 3: View logs

After you configure the log collection feature, Logtail uses the specified project to collect logs of the ingress gateway, and stores the logs in the specified Logstore. To view the logs, perform the following steps:

1.  Log on to the [ASM console](https://servicemesh.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Mesh Management**.

3.  On the **Mesh Management** page, find the ASM instance that you want to configure. Click the name of the ASM instance or click **Manage** in the **Actions** column of the ASM instance.

4.  In the Data Plane section, click the Clusters tab and find the cluster of the ingress gateway for which you want to view logs. Click **View Reports** and then **Ingress Gateway Overview**, **Access from Ingress Gateway**, or **Ingress Gateway Monitoring** in the Observability column.

    -   Click **Ingress Gateway Overview**. The Ingress Gateway Overview page shows statistics of the ingress gateway, including the geographical locations of visitors, page views \(PVs\), unique visitors \(UVs\), and success rate and latency of access requests.

        ![Ingress Gateway Overview](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/3270462061/p133385.png)

    -   Click **Access from Ingress Gateway**. The Access from Ingress Gateway page shows detailed information about PVs, UVs, geographical locations of visitors, and devices of visitors. You can analyze the user distribution and behavior based on the information on this page.

        ![Access from Ingress Gateway](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4270462061/p133390.png)

    -   Click **Ingress Gateway Monitoring**. The Ingress Gateway Monitoring page shows the success rate, status codes, and latency of access requests. You can analyze your service status based on the information on this page.

        ![Ingress Gateway Monitoring](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4270462061/p133391.png)



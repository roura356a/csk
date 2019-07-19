# Deploy Istio on a Kubernetes cluster {#concept_mpq_kkh_1fb .concept}

This topic describes how to use the Container Service console to deploy Istio on a Kubernetes cluster that is created by Alibaba Cloud Container Service for Kubernetes \(ACK\).

## Background information {#section_yq6_hqu_mdy .section}

Istio provides a service mesh that can be used to meet the requirements of the distributed application architecture that involves microservices such as application O&M, debugging, and security management. You also can use Istio for microservice network scenarios such as load balancing, service-to-service authentication, and monitoring.

## Prerequisites {#section_ymx_4kh_1fb .section}

-   A Kubernetes cluster is created with ACK. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   The version of the Kubernetes cluster is 1.10.4 or later.

    **Note:** If you are using a Kubernetes cluster of an earlier version, you must upgrade the cluster to V1.10.4 or later.

-   An Alibaba Cloud account or RAM users \(with sufficient permissions granted to them\) are obtained to log on to Alibaba Cloud. For more information, see [Grant RBAC permissions to a RAM user](reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Grant RBAC permissions to a RAM user.md#).
-   At least three Worker nodes are set for the Kubernetes cluster.

## Procedure {#section_spk_skh_1fb .section}

1.  Deploy Istio on the target Kubernetes cluster.

    1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
    2.  In the left-side navigation pane under Container Service-Kubernetes, choose **Clusters** \> **Clusters**.
    3.  Find the target cluster. Then, in the **Action** column, choose **More** \> **Deploy Istio**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156350842311255_en-US.png)

    4.  Set the following parameters.

        |Setting|Description|
        |-------|-----------|
        |**Clusters**|The target cluster on which Istio is deployed.|
        |**Enable Prometheus for metrics/logs collection**|Indicates whether to enable Prometheus for metrics and logs collection. By default, this is disabled.|
        |**Enable Grafana for metrics display**|Indicates whether to allow Grafana to display metrics. By default, this is disabled.|
        |**Enable the Kiali Visualization Service Mesh**|Indicates whether to enable the Kiali visualization service mesh. By default, this is disabled. To enable this feature, set the following parameters:

         -   **Username**: The default is admin.
        -   **Password**: The default is admin.
 |
        |**Tracing Analysis Settings**|**Activate Tracing Analysis**: Indicates whether to activate the Tracing Analysis service. To activate this service, select this radio button, and then click **Activate now**. You must enter an endpoint address in the format of http://tracing-analysis-dc-hz.aliyuncs.com/.../api/v1/spans. An address of this format indicates an Internet or intranet endpoint that is used by a Zipkin client. This client is used to transmit collected data to the Tracing Analysis service, which then uses the API from v1 release.

 **Note:** If you use an intranet endpoint, you must ensure that your Kubernetes cluster and the [Tracing Analysis](https://tracing-analysis.console.aliyun.com/) instance are in the same region to maintain stable network performance.

 |
        |**Pilot Settings**|The trace sampling percentage. The value range is from 0 to 100. The default value is 1.|
        |**Control Egress Traffic**|         -   **Permitted Addresses for External Access**: Indicates the range of IP addresses that can be used to directly access services in the Istio service mesh. By default, this field is left unspecified. Use commas \(,\) to separate multiple IP address ranges.
        -   **Blocked Addresses for External Access**: Indicates the range of IP addresses that are blocked against external accesses. By default, this IP address range contains the cluster pod CIDR block and service CIDR block. Use commas \(,\) to separate multiple IP address ranges.
        -   **ALL**: Select this check box to block all the IP addresses used to access the Internet.
 **Note:** If the settings of these two parameters conflict with each other, the Permitted Addresses for External Access prevails.

 |

    5.  Click **Deploy Istio**.

        At the bottom of the deployment page, you can view the deployment progress and status in real time.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156350842311256_en-US.png)

    **Verify the results**

    To verify that Istio is deployed on the target Kubernetes cluster, perform the following operations:

    -   View the status of this deployment.

        At the bottom of the Deploy Istio page, verify that **Deploy Istio** is changed to **Deployed**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156350842311257_en-US.png)

    -   View the pods on which Istio is deployed.
        1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Pods**.
        2.  Select the cluster and namespace where Istio is deployed.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156350842311258_en-US.png)

    -   View the services on which Istio is deployed.
        1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Discovery and Load Balancing** \> **Services**.
        2.  Select the cluster and namespace where Istio is deployed.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/156350842311259_en-US.png)

2.  Modify the Istio ingress gateway.

    **Note:** If you deploy Istio 1.1.4 or later on a Kubernetes cluster, an ingress gateway can be automatically created. Therefore, we recommend that you upgrade Istio to the latest version.

    1.  In the left-side navigation pane under Container Service-Kubernetes, choose **Applications** \> **Releases**.
    2.  Click the **Helm** tab.
    3.  In the **Action** column, click **Update**.

        The displayed page shows the settings as follows:

        ``` {#codeblock_al9_tsq_pkc}
        gateways:
          enabled: true
          ingress:
            - enabled: true
              gatewayName: ingressgateway
              maxReplicas: 5
              minReplicas: 1
              ports:
                - name: status-port
                  port: 15020
                  targetPort: 15020
                - name: http2
                  nodePort: 31380
                  port: 80
                  targetPort: 80
                - name: https
                  nodePort: 31390
                  port: 443
                  targetPort: 0
                - name: tls
                  port: 15443
                  targetPort: 15443
              replicaCount: 1
              serviceType: LoadBalancer
          k8singress: {}
        ```

    4.  Modify settings of the ingress gateway, and then click **Update**.

        **Note:** 

        -   replicaCount: Set the number of replicas.
        -   ports: Set ports.
        -   serviceType: Set the service type. Valid value: `LoadBalancer` | `ClusterIP` | `NodePort`.
        -   serviceAnnotations: \(If you set serviceType as `LoadBalancer`, this parameter is available.\) Set whether to use an Internet or intranet SLB instance, and whether to use an existing SLB instance or create a new SLB instance. For more information, see [Server Load Balancer](https://www.alibabacloud.com/help/doc-detail/86397.htm?spm=a2c63.p38356.b99.205.27401c4aH49A7s).


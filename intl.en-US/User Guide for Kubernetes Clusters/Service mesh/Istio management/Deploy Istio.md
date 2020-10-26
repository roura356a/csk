---
keyword: [deploy Istio, microservice networks]
---

# Deploy Istio

Istio provides a service mesh that fills in gaps of a distributed microservice architecture in terms of O&M, debugging, and security management. This allows you to deploy microservice networks in a simplified manner. Istio supports load balancing, service-to-service authentication, and monitoring. You do not have to change microservices to enable these features.

-   A Kubernetes cluster is created. For more information, see [Create a cluster of ACK Managed Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Managed Edition.md).
-   An Alibaba Cloud account or a RAM user that has required permissions, for example, a RAM user that has the custom role cluster-admin, is obtained to log on to the Container Service for Kubernetes \(ACK\) console. For more information, see [Configure RBAC permissions for RAM users](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Configure RBAC permissions for RAM users.md).

-   The cluster version is V1.10.4 or later. If the cluster is in an earlier version, you must upgrade the cluster to V1.10.4 or later.
-   A minimum of three worker nodes are configured for the cluster. This ensures sufficient resources.

1.  Deploy Istio on a cluster.

    1.  Log on to the [ACK console](https://cs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Service Mesh** \> **Istio Management**.

    3.  Configure the parameters for Istio.

        |Parameter|Description|
        |---------|-----------|
        |Cluster|The cluster where you want to deploy Istio.|
        |Namespace|By default, the namespace is istio-system.|
        |Release Name|By default, the release name is Istio.|
        |Latest Version|The latest version of Istio.|
        |Pilot Settings|**Trace Sampling Percentage \(Valid values: 0 to 100\)**: By default, the value is 100.

**Enable Locality Load Balancing**: Istio enables global load balancing to distribute network traffic to microservices through Envoy proxies. You can deploy microservice instances to Kubernetes clusters across multiple regions. Istio collects information about microservice instances, such as the running statuses, routing, and backend servers, and then sends the information to Envoy proxies. This allows Envoy proxies to distribute network traffic along the optimal routes to microservice instances that are deployed across multiple regions. Istio sorts these microservice instances in ascending order of the distances between the Envoy proxies and the clients that send requests to the instances. The microservice instance that is closest to the clients can receive the requests in priority. After this feature is enabled, if all microservice instances run properly, the routes along which requests are forwarded to the instances are not changed. |
        |Control Egress Traffic|        -   **CIDR Blocks for Internal Access**: the range of IP addresses that cannot be accessed by services in the Istio mesh. By default, this parameter value contains the pod CIDR block and service CIDR block. Use commas \(,\) to separate multiple CIDR blocks.

**All**: Select this check box to block all the IP addresses from being accessed.

        -   **CIDR Blocks for External Access**: the range of IP addresses that can be accessed by services in the Istio mesh. Use commas \(,\) to separate multiple CIDR blocks. By default, this parameter is left unspecified.
**Note:** The setting of CIDR Blocks for External Access takes priority over that of CIDR Blocks for Internal Access.

For example, if an IP address is contained in the values of both parameters, the setting of CIDR Blocks for External Access prevails. This IP address can be accessed by services in the Istio mesh. |
        |Gateway|Specifies whether to create a default ingress gateway. To create a default ingress gateway, select the **Create Default Ingress Gateway** check box and set the following parameters:         -   **SLB Network Type**: Select **Public Network** or **Internal Network**.
        -   **Use Existing SLB Instance**: Select a Server Load Balancer \(SLB\) instance from the drop-down list. If no SLB instance is available, click **Create SLB** next to the Use Existing SLB Instance drop-down list. For more information, see [Create an SLB instance](/intl.en-US/Quick Start (New Console)/Create an SLB instance.md).

**Note:** If you use an existing SLB instance, the existing listeners of the SLB instance are overwritten.

        -   **Port Mapping**: Set **Service Port** and **Container Port**. |
        |Certificate Management|Select or clear **Enable cert-manager**. You can use cert-manager to retrieve certificates by using key pairs stored in Kubernetes secrets.|
        |Observability|If you select **Enable Prometheus for Metrics/Logs Collection**, the following options are available:         -   **Install Prometheus**:

If you select this option, you can select **Persistent Storage**, and set the TSDB Endpoint field.

**Note:** To enable persistent storage, log on to the [Time Series Database \(TSDB\) console](https://tsdb.console.aliyun.com/?spm=5176.2020520152.0.0.21bd16ddl1nNb7#/cluster/cn-hangzhou) and activate the TSDB service.

        -   **Use Existing Prometheus**: If you select this option, you must enter the Prometheus service endpoint in the Endpoint field.

If the Prometheus service and Istio are deployed on the same cluster, you can set the Prometheus service endpoint in this format: \[Service name\].\[ Namespace\]:\[Port\], for example, http://prometheus-default-server.mygw1:80. If the Prometheus service and Istio are not deployed on the same cluster, you must provide the IP address that can be accessed by Istio, for example, http://1.2.3.4:9090. |
        |If you select **Enable Grafana for Metrics Display**, you must set the Username and Password fields. By default, both fields are set to admin.|
        |If you select **Enable Kiali Visualized Service Mesh**, you must set the Username and Password fields. By default, both fields are set to admin.|
        |Tracing Analysis Settings|Specifies whether to activate the Tracing Analysis service. To activate the service, select **Activate Tracing Analysis \(Activate Now\)**, and specify the endpoint of the Tracing Analysis service. For example, you can specify http://tracing-analysis-dc-hz.aliyuncs.com/.../api/v1/spans as the endpoint. This is a public endpoint or an internal endpoint of the Zipkin API V1. After you enable the Tracing Analysis service, a Zipkin client uses the endpoint to transmit collected data to the Tracing Analysis service.**** **Note:** If you use an internal endpoint, make sure that your Kubernetes cluster and the [Tracing Analysis instance](https://tracing-analysis.console.aliyun.com/) are deployed in the same region. In this case, the cluster and the Tracing Analysis instance can communicate with each other. |

    4.  Click **Deploy Istio**. In the **Message** dialog box, click **OK**.

        At the bottom of the deployment page, you can view the deployment progress and status in real time.

        ![Verify the deployment result](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8555359951/p11256.png)

    You can check whether Istio is deployed on the cluster by using one of the following methods:

    -   At the bottom of the Deploy Istio page, check whether the **Deploy Istio** step is changed to **Deployed**.

        ![Verify that Istio is deployed](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8555359951/p11257.png)

    -   On the Clusters page, find the cluster that you want to manage and click the cluster name or click **Details** in the **Actions** column. In the left-side navigation pane, click **Workload**. Select the cluster and the namespace where you deploy Istio and click the **Pods** tab. On the tab that appears, view the pods where Istio is deployed.

        ![Pods](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0370963061/p147477.png)

    -   On the Clusters page, find the cluster that you want to manage and click the cluster name or click **Details** in the **Actions** column. In the left-side navigation pane, click **Services**. Select the cluster and the namespace where you deploy Istio. On the Services page, view the IP addresses of services where Istio is deployed.

        ![Services](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/0370963061/p147481.png)

2.  Manage an Istio ingress gateway.

    When you deploy Istio, you can select the **Create Default Ingress Gateway** check box to deploy an ingress gateway. If you use an existing ingress gateway, we recommend that you upgrade the ingress gateway to the latest version. To modify the configurations of an ingress gateway, perform the following steps.

    1.  In the left-side navigation pane, click **Clusters**.

    2.  On the Clusters page, find the cluster that you want to manage and click the cluster name or click **Details** in the **Actions** column.

    3.  In the left-side navigation pane, click **Releases** and click the **Helm** tab.

    4.  Click Update in the **Actions** column for Istio. In the Update Release dialog box, you can view the configurations of the ingress gateway.

        ```
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

    5.  Modify the configurations of the ingress gateway and click **Update**.

        **Note:**

        -   replicaCount: the number of replicas.
        -   ports: the ports that you want to enable.
        -   serviceType: the type of the service. Valid values: LoadBalancer, ClusterIP, and NodePort.
        -   serviceAnnotations: specifies whether to use a public-facing SLB instance or an internal SLB instance, or whether to use an existing SLB instance or create an SLB instance. This parameter is valid only when the serviceType parameter is set to LoadBalancer. For more information, see [Use annotations to configure SLB instances](/intl.en-US/User Guide for Kubernetes Clusters/Network management/Service Management/Use annotations to configure SLB instances.md).
3.  Add a gateway.

    If you deploy Istio on a cluster, an Istio ingress gateway is installed. If you upgrade Istio, all default Istio resources including the default ingress gateway are automatically upgraded.

    If you add ingress or egress gateways, you can manage these gateways and the system does not modify the gateway configurations during the automatic upgrade of Istio. By default, for Istio 1.1 or later, the Istio egress gateway is not installed.

    Do not change the configurations of the default ingress gateway. After the automatic upgrade of Istio, the changes are restored to the default configurations. To meet more complex requirements, create a custom ingress gateway.


**Related topics**  


[Update Istio](/intl.en-US/User Guide for Kubernetes Clusters/Service mesh/Istio management/Update Istio.md)


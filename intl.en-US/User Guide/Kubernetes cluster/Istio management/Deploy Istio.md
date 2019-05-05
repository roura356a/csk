# Deploy Istio {#concept_mpq_kkh_1fb .concept}

The distributed application architecture composed of microservices has disadvantages in aspects such as operation and maintenance \(O&M\), debugging, and security management. To eliminate the disadvantages, you can deploy Istio to create microservice network and to provide load balancing, service-to-service authentication, monitoring, and other functions. Istio provides the functions without requiring any changes to services.

## Prerequisites {#section_ymx_4kh_1fb .section}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have logged on to the Container Service console by using the primary account or by using a sub-account that has been granted sufficient permissions. For example, if the cluster-admin permission is granted to a sub-account then Istio can be deployed. Other combinations of permissions are also sufficient. For more information, see [Grant Kubernetes permissions to a RAM user](reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Grant Kubernetes permissions to a RAM user.md#).

## Background information {#section_f1c_rkh_1fb .section}

-   Alibaba Cloud Container Service for Kubernetes in versions of 1.10.4 and later support Istio deployment. If your Container Service for Kubernetes is in any version prior to 1.10.4, update the version to 1.10.4 or later.

-   To guarantee sufficient resources, the number of Worker nodes in a cluster must be greater than or equal to 3.


## Procedure {#section_spk_skh_1fb .section}

You can deploy Istio through the Clusters page or through the App Catalog page.

-   **Deploy Istio through the Clusters page**
    1.  Deploy Istio.

        1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
        2.  In the left-side navigation pane, choose **Clusters** \> **Clusters**.
        3.  On the right of the target cluster, choose **More** \> **Deploy Istio**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045911255_en-US.png)

        4.  Set the following Istio parameters.

            |Configuration|Description|
            |-------------|-----------|
            |Clusters|Target cluster in which Istio is deployed.|
            |Namespace|Namespace in which Istio is deployed.|
            |Release Name|Name of Istio to be released.|
            |Enable Prometheus for metrics/logs collection|Whether to enable Prometheus for metrics/logs collection. Enabled by default.|
            |Enable Grafana for metrics display|Whether to enable Grafana for metrics display. Enabled by default.|
            |Enable automatic Istio Sidecar injection|Whether to enable automatic Istio Sidecar injection. Enabled by default.|
            |Enable the Kiali Visualization Service Mesh|Whether to enable the Kiali Visualization Service Mesh. Disabled by default.            -   Username: Set a user name. The default is admin.
            -   Password: Set a password. The default is admin.
|
            |Tracing Analysis Settings|             -   **Enable Distributed Tracing with Jaeger**: indicates whether to enable Jaeger \(the distributed tracing system\). To use Jaeger, select this radio button, and activate Alibaba Cloud Log Service.

**Note:** If you select this radio button, Log Service will automatically create a project named istio-tracing-\{ClusterID\} that is used to store the tracking data.

            -   **Activate Tracing Analysis**: inditcates whether to activate the Tracing Analysis service. To activate this service, select this radio button, and then click **Activate now**. Additionally, you need to enter an endpoint address in the format of http://tracing-analysis-dc-hz.aliyuncs.com/.../api/v1/spans.

**Note:** 

                -   An address of this format indicates an Internet or intranet endpoint used by a Zipkin client to transmit collected data in a region to the Tracing Analysis service that uses the API from v1 release.

                -   If you use an intranet endpoint, you must ensure that your Kubernetes cluster and the [Tracing Analysis](https://tracing-analysis.console.aliyun.com/) instance are in the same region to maintain stable network performance.
 |
            |Pilot Settings|Set the trace sampling percentage in the range of 0 to 100. The default value is 1.|
            |Control Egress Traffic|             -   **Permitted Addresses for External Access**: range of IP addresses that can be used to directly access services in the Istio service mesh. By default, this field is left blank. Use commas \(,\) to separate multiple IP address ranges.
            -   **Blocked Addresses for External Access**: range of IP addresses that are blocked against external accesses. By default, this IP address range contains the cluster pod CIDR block and service CIDR block. Use commas \(,\) to separate multiple IP address ranges.
            -   **ALL**: Select this check box to block all the IP addresses used to access the Internet.
 **Note:** 

If the settings of these two parameters conflict with each other, the Permitted Addresses for External Access prevails.

For example, if an IP address is listed in both IP address ranges that you set for these two parameters, the IP address can be still accessed. That is, the setting of Permitted Addresses for External Access prevails.

 |

        5.  Click **Deploy Istio**.

            At the bottom of the deployment page, you can view the deployment progress and status in real time.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045911256_en-US.png)

        **Verify the result**

        You can view your deployment results in the following ways:

        -   At the bottom of the Deploy Istio page, **Deploy Istio** is changed to **Deployed**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045911257_en-US.png)

        -   -   In the left-side navigation pane, choose **Application** \> **Pods**.
-   Select the cluster and namespace in which Istio is deployed, and you can see the relevant pods in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045911258_en-US.png)

        -   -   In the left-side navigation pane, choose **Application** \> **Service**.
-   Select the cluster and namespace in which Istio is deployed, and you can see the access addresses provided by the relevant services in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045911259_en-US.png)

    2.  Create the Istio Ingress gateway.

        **Note:** By default, the system does not create the Istio Ingress gateway after you have deployed Istio.

        1.  In the left-side navigation pane, choose **Store** \> **App Catalog**.
        2.  Click **ack-istio-ingressgateway**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045941685_en-US.png)

        3.  Click the **Values** tab, and then set the parameters.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045941686_en-US.png)

            **Note:** 

            -   For more information about the description, the values, and the default parameters, see the **Configuration** section on the **Readme** tab page.
            -   You can set customized parameters, including indicating whether to enable a specific port, or whether to use the intranet SLB or the Internet SLB by setting the serviceAnnotations parameter.
        4.  In the Deploy area on the right, select the target **Cluster** from the drop-down list, and then click DEPLOY.

            **Note:** **Namespace** is fixed as istio-system, and **Release Name** is fixed as istio-ingressgateway.

        Verify the result.

        1.  In the left-side navigation pane, choose **Application** \> **Pod**.
        2.  Select the target cluster and the **istio-system** namespace to view the pod to which the Istio Ingress gateway has been deployed.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045941687_en-US.png)

-   **Deploy Istio through the App Catalog page**
    1.  Deploy the CRD of Istio.

        1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
        2.  In the left-side navigation pane, choose **Store** \> **App Catalog**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045941697_en-US.png)

        3.  Click **ack-istio-init**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045941698_en-US.png)

        4.  In the Deploy area on the right, select the target **Cluster** from the drop-down list, and then click DEPLOY.

            **Note:** **Namespace** is fixed as istio-system, and **Release Name** is fixed as istio-init.

        Verify the result.

        1.  In the left-side navigation pane, choose **Application** \> **Pods**.
        2.  Select the target cluster and the **istio-system** namespace to view the pod to which the Istio CRD has been deployed.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045911258_en-US.png)

    2.  Deploy Istio.

        1.  In the left-side navigation pane, choose **Store** \> **App Catalog**.
        2.  Click **ack-istio**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704046041679_en-US.png)

        3.  Click the Values tab, and then set the parameters.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704046041681_en-US.png)

            **Note:** 

            -   For more information about the description, the values, and the default parameters, see the **Configuration** section on the **Readme** tab page.
            -   You can set customized values for parameters, including the grafana, prometheus, tracing, kiali, and other parameters, to better meet your requirements.
        4.  In the Deploy area on the right, select the target **Cluster** from the drop-down list, and then click DEPLOY.

            **Note:** **Namespace** is fixed as istio-system, and **Release Name** is fixed as istio.

        5.  Click **DEPLOY**.
        Verify the result.

        -   Verify that Istio has been deployed to a pod.

1.  In the left-side navigation pane, choose **Application** \> **Pods**.
2.  Select the target cluster and namespace to view the pod to which Istio has been deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045911258_en-US.png)

        -   Verify that Istio has been deployed to a service.

1.  In the left-side navigation pane, choose **Application** \> **Service**.
2.  Select the cluster and namespace in which Istio is deployed to view the IP addresses provided by the services to which Istio has been deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045911259_en-US.png)

    3.  Create the Istio Ingress gateway.

        **Note:** By default, the system does not create the Istio Ingress gateway after you have deployed Istio.

        1.  In the left-side navigation pane, choose **Store** \> **App Catalog**.
        2.  Click **ack-istio-ingressgateway**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045941685_en-US.png)

        3.  Click the **Values** tab, and then set the parameters.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045941686_en-US.png)

            **Note:** 

            -   For more information about the description, the values, and the default of the parameters, see the **Configuration** section on the **Readme** tab page.
            -   You can set customized parameters. For example, you can enable a specific port, or use the intranet SLB or the Internet SLB by set the serviceAnnotations parameter.
        4.  In the Deploy area on the right, select the target **Cluster** from the drop-down list, and then click DEPLOY.

            **Note:** **Namespace** is fixed as istio-system, and **Release Name** is fixed as istio-ingressgateway.

        Verify the result.

        1.  In the left-side navigation pane, choose **Application** \> **Pod**.
        2.  Select the target cluster and the **istio-system** namespace to view the pod to which the Istio Ingress gateway has been deployed.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155704045941687_en-US.png)



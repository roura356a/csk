# Deploy Istio {#concept_mpq_kkh_1fb .concept}

The distributed application architecture composed of microservices has disadvantages in aspects such as operation and maintenance \(O&M\), debugging, and security management. To eliminate the disadvantages, you can deploy Istio to create microservice network and to provide load balancing, service-to-service authentication, monitoring, and other functions. Istio provides the functions without requiring any changes to services.

## Prerequisites {#section_ymx_4kh_1fb .section}

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have logged on to the Container Service console by using the primary account or by using a sub-account that has been granted sufficient permissions. For example, if the cluster-admin permission is granted to a sub-account then Istio can be deployed. Other combinations of permissions are also sufficient. For more information, see [Kubernetes permission configuration guide for RAM users](reseller.en-US/User Guide/Kubernetes cluster/Authorization management/Kubernetes permission configuration guide for RAM users.md#).

## Background information {#section_f1c_rkh_1fb .section}

-   Alibaba Cloud Container Service for Kubernetes in versions of 1.10.4 and later support Istio deployment. If your Container Service for Kubernetes is in any version prior to 1.10.4, update the version to 1.10.4 or later.

-   To guarantee sufficient resources, the number of Worker nodes in a cluster must be greater than or equal to 3.


## Procedure {#section_spk_skh_1fb .section}

**Deploy Istio through a cluster interface**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, click **Clusters**.
3.  Select a cluster and then click **More** \> **Deploy Istio** in the action column at the right of a cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385711255_en-US.png)

4.  Deploy Istio according to the following information.

    |Configuration|Description|
    |-------------|-----------|
    |Clusters|Target cluster in which Istio is deployed.|
    |Namespace|Namespace in which Istio is deployed.|
    |Release Name|Name of Istio to be released.|
    |Enable Prometheus for metrics/logs collection|Whether to enable Prometheus for metrics/logs collection. Enabled by default.|
    |Enable Grafana for metrics display|Whether to enable Grafana for metrics display. Enabled by default.|
    |Enable automatic Istio Sidecar injection|Whether to enable automatic Istio Sidecar injection. Enabled by default.|
    |Enable the Kiali Visualization Service Mesh|Whether to enable the Kiali Visualization Service Mesh. Disabled by default.    -   Username: Set a user name. The default is admin.
    -   Password: Set a password. The default is admin.
|
    |Enable Log Service\(SLS\) and Jaeger| Whether to enable Log Service\(SLS\) and Jaeger. Disabled by default.

 Endpoint: Select an address according to the region in which the configured Log Service exists. For more information, see [Service endpoint](../../../../../reseller.en-US/API Reference/Service endpoint.md#section_nv4_h2r_12b).

 Project: Set the name of the project in which you collect logs.

 Logstore: Set the name of the Logstore in which you collect logs.

 AccessKeyID: Set the ID of the AccessKey used to access Log Service.

**Note:** Select the AccessKeyID that has permission to access Log Service.

 AccessKeySecret: Set the AccessKeySecret used to access Log Service.

 |
    |Pilot Settings|Set the trace sampling percentage in the range of 0 to 100. The default is 1.|
    |Control Egress Traffic|     -   Permitted Addresses for External Access: range of IP addresses that can be used to directly access services in the Istio service mesh. By default, this field is left blank. Use commas \(,\) to separate multiple IP address ranges.
    -   Blocked Addresses for External Access: range of IP addresses that are blocked against external accesses. By default, this IP address range contains the cluster pod CIDR block and service CIDR block. Use commas \(,\) to separate multiple IP address ranges.
 **Note:** 

If the settings of these two parameters conflict with each other, the Permitted Addresses for External Access prevails.

For example, if an IP address is listed in both IP address ranges that you set for these two parameters, the IP address can be still accessed. That is, the setting of Permitted Addresses for External Access prevails.

 |

5.  Click **Deploy Istio** to start deployment.

    At the bottom of the deployment page, you can view the deployment progress and status in real time.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385711256_en-US.png)


**Expected results:**

You can view your deployment results in the following ways:

-   At the bottom of the Deploy Istio page, **Deploy Istio** is changed to **Deployed**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385711257_en-US.png)

-   -   In the left-side navigation pane, click **Application** \> **Pods**.
-   Select the cluster and namespace in which Istio is deployed, and you can see the relevant pods in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385711258_en-US.png)

-   -   In the left-side navigation pane, click **Application** \> **Service**.
-   Select the cluster and namespace in which Istio is deployed, and you can see the access addresses provided by the relevant services in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385811259_en-US.png)


**Use the application catalog to deploy Istio**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  In the left-side navigation pane, click **Store** \> **App Catalog**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385811260_en-US.png)

3.  Click **ack-istio**.
4.  Click the Values tab to configure parameters.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385812560_en-US.png)

    **Note:** 

    -   For information about the description, values, and defaults of general parameters, see the **Configuration** section on the Readme tab page.
    -   You can also customize parameters. For example, whether to start grafana, prometheus, tracing, weave-scope, and kiali. See the following:

        ```
        #
        # addons configuration
        #
        grafana:
        enabled: true
        replicaCount: 1
        image: istio-grafana
        service:
        name: http
        type: ClusterIP
        externalPort: 3000
        internalPort: 3000
        ....
        prometheus:
        enabled: true
        replicaCount: 1
        image:
        repository: registry.cn-hangzhou.aliyuncs.com/aliacs-app-catalog/istio-prometheus
        tag: latest
        ....
        tracing:
        enabled: true
        jaeger:
        enabled: true
        ....
        weave-scope:
        enabled: true
        global:
        # global.image: the image that will be used for this release
        image:
        repository: weaveworks/scope
        tag: "1.9.0"
        # global.image.pullPolicy: must be Always, IfNotPresent, or Never
        pullPolicy: "IfNotPresent"
        ....
        kiali:
        enabled: true
        replicaCount: 1
        image:
        repository: registry.cn-hangzhou.aliyuncs.com/aliacs-app-catalog/istio-kiali
        tag: dev
        
        
        ```

5.  In the Deploy section on the right, configure the following.

    |Configuration|Description|
    |-------------|-----------|
    |Clusters|Target cluster in which Istio is deployed.|
    |Namespace|Namespace in which Istio is deployed. The default namespace is default.|
    |Release Name|Name of Istio to be released.|

6.  Click **DEPLOY** to start deployment.

**Expected results:**

-   -   In the left-side navigation pane, click **Application** \> **Pods**.
-   Select the cluster and namespace in which Istio is deployed, and you can see the relevant pods in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385711258_en-US.png)

-   -   In the left-side navigation pane, click **Application** \> **Service**.
-   Select the cluster and namespace in which Istio is deployed, and you can see the access addresses provided by the relevant services in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/155374385811259_en-US.png)



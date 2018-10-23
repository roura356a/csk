# Deploy Istio {#concept_mpq_kkh_1fb .concept}

To solve problems for the distributed application architecture composed of microservices in dimensions of operation and maintenance, debugging, and security management. you can deploy Istio to create microservice network and to provide load balancing, authentication between services, and monitoring. Istio provides the preceding functions without requiring changes to services.

## Prerequisites {#section_ymx_4kh_1fb .section}

You have created a Kubernetes cluster. For how to create a Kubernetes cluster, see [Create a Kubernetes cluster](reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).

## Background information {#section_f1c_rkh_1fb .section}

-   Alibaba Cloud Container Service for Kubernetes in versions of 1.10.4 and later support Istio deployment. If your Container Service for Kubernetes is in any version prior to 1.10.4, update the version to 1.10.4 or later.

-   To guarantee sufficient resources, the number of worker nodes in a cluster must be greater than or equal to 3.


## Procedure {#section_spk_skh_1fb .section}

**Deploy Istio through a cluster Interface**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Cluster** in the left-hand navigation pane.
3.  Click **More** \> **Deploy Istio** in the action column at the right of a cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847611255_en-US.png)

4.  Deploy Istio according to the following information:

    |Configuration|Description|
    |-------------|-----------|
    |Clusters|The target cluster for to which Istio is deployed.|
    |Namespace|The namespace in which Istio is deployed.|
    |Release Name|The name of Istio to be released.|
    |Enable Prometheus for metrics/logs collection|Whether to enable Prometheus for metrics/logs collection. Enabled by default.|
    |Enable Grafana for metrics display|Whether to enable Grafana for metrics display. Enabled by default.|
    |Enable ServiceGraph for deployment visualization|Whether to enable ServiceGraph for deployment visualization. Enabled by default.|
    |Enable automatic Istio Sidecar injection|Whether to enable automatic Istio Sidecar injection Enabled by default.|
    |Enable Log Service\(SLS\) and Jaeger| Whether to enable Log Service\(SLS\) and Jaeger This parameter is not enabled by default.

 Endpoint: Select an address based on the region in which the configured Log Service exists. For more information, see [Service endpoint](../../../../reseller.en-US/API Reference/Service endpoint.md#section_nv4_h2r_12b).

 Project: The name of the project in which you collect logs.

 Logstore: The name of the Logstore in which you collect logs.

 AccessKeyID: The ID of the Access Key used to access Log Service.

**Note:** Select the AccessKeyID that has permission to access Log Service.

 AccessKeySecret: The Access Key Secret used to access Log Service.

 |

5.  Click **Deploy Istio** to start deployment.

    At the bottom of the deployment page, you can view the deployment progress and status in real time.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847611256_en-US.png)


**Expected results:**

You can view your deployment results in the following ways:

-   At the bottom of the Deploy Istio page,**Deploy Istio** is changed to **Deployed**.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847611257_en-US.png)

-   -   Click **Application** \> **Pods** in the left-side navigation pane.
-   Select the cluster and namespace in which Istio is deployed, and you can see the relevant pods in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847611258_en-US.png)

-   -   Click **Application** \> **Service** in the left-side navigation pane.
-   Select the cluster and namespace in which Istio is deployed, and you can see access addresses provided by the relevant services in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847611259_en-US.png)


**Use the application catalog to deploy Istio**

1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
2.  Click **Store** \> **App Catalog** in the left-side navigation pane.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847711260_en-US.png)

3.  Click **ack-istio**.
4.  Click the Values tab to configure parameters.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847712560_en-US.png)

    **Note:** 

    -   For information about the description, values, and defaults of general parameters, see the **Configuration** section in the Readme tab.
    -   You can also customize parameters. For example, whether to startgrafana,prometheus, tracing, weave-scope, and kiali, see the following:

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

5.  In the Deploy section on the right, configure the following:

    |Configuration|Description |
    |-------------|------------|
    |Clusters|The target cluster in which Istio is deployed.|
    |Namespace|The namespace in which Istio is deployed. Default is default.|
    |Release Name|The name of Istio to be released.|

6.  Click **DEPLOY** to start deployment.

**Expected results:**

-   -   Click **Application** \> **Pods** in the left-side navigation pane.
-   Select the cluster and namespace in which Istio is deployed, and you can see the relevant pods in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847611258_en-US.png)

-   -   Click **Application** \> **Service** in the left-side navigation pane.
-   Select the cluster and namespace in which Istio is deployed, and you can see access addresses provided by the relevant services in which Istio is deployed.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20172/154025847611259_en-US.png)



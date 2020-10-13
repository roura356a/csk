# Use ASM to balance the load for an application deployed in clusters across regions

Alibaba Cloud Service Mesh \(ASM\) uses Envoy proxies to ensure global load balancing among application services in a service mesh. Envoy proxies can route traffic to the optimal instances deployed in different regions. This topic uses the Bookinfo as an example to describe how to use ASM to implement global load balancing for an application that is deployed in two clusters across regions.

-   An ASM instance is created. For more information, see [Create an ASM instance](). In this topic, the ASM instance is mesh1.
-   The IPv4 classless inter-domain routing \(CIDR\) blocks of the VSwitches to be used by the two clusters do not overlap, and the pod and service CIDR blocks do not overlap.
-   A Container Service for Kubernetes cluster is created in two Virtual Private Clouds \(VPCs\), respectively. For more information, see [Create a cluster of ACK Proprietary Edition](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Create Kubernetes clusters/Create a cluster of ACK Proprietary Edition.md). In this topic, the two clusters are m1c1 and m1c2.
-   [Cloud Enterprise Network \(CEN\)](https://cen.console.aliyun.com/) is activated.
-   The two VPCs can be connected through CEN. For more information, see [Tutorial overview]().
-   All CIDR blocks are published to CEN and do not conflict with each other. For more information, see [Publish a route to CEN]().

-   The following table describes the network plan in this topic.

    |Object|Cluster m1c1|Cluster m1c2|
    |------|------------|------------|
    |IPv4 CIDR block of the VPC or VSwitch|192.168.0.0/24|192.168.1.0/24|
    |Pod CIDR block|172.22.0.0/16|172.30.0.0/16|
    |Service CIDR block|172.23.0.0/20|172.31.0.0/20|

-   In a Container Service for Kubernetes cluster, the location of a pod is determined by the region and zone labels of the node where the pod resides. For more information, see [Well-Known Labels, Annotations and Taints](https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/#failure-domainbetakubernetesioregion). By default, Container Service for Kubernetes clusters are configured with these settings.

## Step 1: Deploy an application in clusters across regions

Deploy the Bookinfo application in multiple clusters in VPCs across regions.

## Step 2: Enable access to the nearest instance

To detect abnormal microservice instances of the Bookinfo application in a timely manner, you need to use Envoy proxies to configure exception detection in the destination rule for each service. For more information, see [Destination Rule](https://istio.io/docs/reference/config/networking/destination-rule/#OutlierDetection). When an Envoy proxy sends a request, ASM prioritizes the workload instances of the target application service based on the location of the Envoy proxy. If you enable the access to the nearest instance and all workload instances are normal, requests from Envoy proxies are preferentially sent to the nearest instances.

1.  Log on to the [ASM console](https://servicemesh.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Mesh Management**.

3.  On the **Mesh Management** page, click the name of the target ASM instance or click **Manage** in the **Actions** column.

4.  In the **Basic Information** section, click **Settings** in the upper-right corner.

5.  In the **Settings Update** pane, select **Enable Access to Nearest Nodes**.

6.  Click **OK**.

    In the **Basic Information** section, you can find that the status in the **Access Nearest Nodes** field changes to **Enable**.


No destination rule with exception detection is configured for the reviews microservice. You can refresh the product page to view the effect of the three versions of the reviews microservice in turn.

-   Version v1 does not call the Ratings service.
-   Version v2 calls the Ratings service and displays each rating as 1 to 5 black stars.
-   Version v3 calls the Ratings service and displays each rating as 1 to 5 red stars.

## Step 3: Configure exception detection

Requests are sent to the workload instance that is nearest to the source Envoy proxy only when the workload instance runs normally. Therefore, you need to configure exception detection in the destination rule for the target microservice so that Envoy proxies can check whether the nearest workload instance is normal.

1.  Log on to the [ASM console](https://servicemesh.console.aliyun.com).

2.  In the left-side navigation pane, choose **Service Mesh** \> **Mesh Management**.

3.  On the **Mesh Management** page, click the name of the target ASM instance or click **Manage** in the **Actions** column.

4.  In the **Control Plane** section, click the **Destination Rule** tab and then **Create**.

5.  In the **Create** pane, define a destination rule and click **OK**.

    1.  In the **Namespace** drop-down list, select the namespace in which you want to define the destination rule.

    2.  In the code editor, enter the configuration information for the destination rule.

        The content of the YAML file is as follows:

        ```
        apiVersion: networking.istio.io/v1alpha3
        kind: DestinationRule
        metadata:
          name: reviews
        spec:
          host: reviews
          trafficPolicy:
            outlierDetection:
              consecutiveErrors: 5
              interval: 2m
              baseEjectionTime: 5m
        ```

    On the **Destination Rule** tab, you can view the defined destination rule.


The productpage microservice and the v1 and v2 versions of the reviews microservice are deployed in the same cluster. The v3 version of the reviews microservice is deployed in another cluster in a different region. When you enable the access to the nearest instance and the v1 and v2 versions of the reviews microservice are normal, traffic for accessing the reviews microservice will not be routed to the v3 version. You can refresh the product page to view the effect of the v1 and v2 versions of the reviews microservice in turn.

-   Version v1 does not call the Ratings service.
-   Version v2 calls the Ratings service and displays each rating as 1 to 5 black stars.

**Note:** You can reduce the number of pods to 0 to disable the v1 and v2 versions of the reviews microservice deployed in the m1c1 cluster. Then, when you refresh the product page again, you can find that ratings on the page are displayed as 1 to 5 red stars. This indicates that the v3 version of the reviews microservice takes effect.


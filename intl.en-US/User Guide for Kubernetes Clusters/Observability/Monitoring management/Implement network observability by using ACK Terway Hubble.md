---
keyword: [ACK Terway Hubble, container network, network traffic, network observability]
---

# Implement network observability by using ACK Terway Hubble

Container Service for Kubernetes \(ACK\) Terway Hubble is a network architecture, workload, and topology observability platform. You can deploy ACK Terway Hubble in a managed Kubernetes cluster and then view network traffic and network policies in ACK Terway Hubble. This topic describes how to use the network observability of ACK Terway Hubble to view statistics about network traffic in a container network, such as the sources and destinations of packets.

[Create a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)

**Note:** ACK Terway Hubble supports only the One ENI for Multi-Pod mode of Terway. This mode is based on IPvlan. Therefore, when you create the managed Kubernetes cluster, you must specify **Terway** as **Network Plug-in** and **IPvlan** as **Terway Mode**. Otherwise, you cannot use ACK Terway Hubble.

## Step 1: Modify the Terway ConfigMap eni-config

**Modify the Terway ConfigMap eni-config in the console**

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  Modify the Terway ConfigMap eni-config.

    1.  In the left-side navigation pane of the details page, choose **Configurations** \> **ConfigMaps**.

    2.  On the top of the **ConfigMap** page, select kube-system as **Namespace**, and click **Edit YAML** in the **Actions** column for eni-config.

    3.  In the **View in YAML** panel, find `10-terway.conf`. Then, set the following parameters under `10-terway.conf` and click **OK**.

        ```
        "cilium_enable_hubble":"true",
        "cilium_hubble_listen_address":":4244",
        "cilium_hubble_metrics_server":":9091",
        "cilium_hubble_metrics":"drop,tcp,flow,port-distribution,icmp",
        ```

        |Parameter|Description|Remarks|
        |---------|-----------|-------|
        |eniip\_virtual\_type|Specifies whether to enable the IPvlan mode.|If the ConfigMap does not contain this parameter or the value is not set to `IPVLAN`, your cluster does not support ACK Tereway Hubble.|
        |cilium\_enable\_hubble|Specifies whether to enable ACK Terway Hubble to analyze network traffic.|This parameter is set to `"true"` in this example.|
        |cilium\_hubble\_listen\_address|Specifies the port that is used to expose the Hubble Service.|This parameter is set to `":4244"` in this example.|
        |cilium\_hubble\_metrics\_server|Specifies the port that is used to expose the Hubble metrics server.|This parameter is set to `":9091"` in this example.|
        |cilium\_hubble\_metrics|Separate the Hubble metrics with commas \(,\).|Layer 7 network capabilities such as HTTP and DNS are not supported. ACK Terway Hubble can collect the following metrics: `"drop,tcp,flow,port-distribution,icmp"`. **Note:** If you specify excessive metrics, the performance of ACK Terway Hubble may be degraded. |

5.  Restart the Terway pods for the modified ConfigMap to take effect.

    1.  On the top of the **ConfigMap** page, select kube-system as **Namespace**. Then, enter terway-eniip into the search box, and click **Delete** in the **Actions** column for terway-eniip-xxx.

    2.  In the **Delete Pod** message, click **OK**.

        On the **Pods** page, if the **Status** column of terway-eniip-xxx displays **Running**, this indicates that the pod is restarted.

    3.  Repeat the preceding steps to delete all Terway pods.


**Modify the Terway ConfigMap eni-config by using the CLI**

1.  [Connect to an ACK cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to Kubernetes clusters by using kubectl.md).

2.  Modify the Terway ConfigMap eni-config.

    1.  Run the following command to modify the Terway ConfigMap eni-config:

        ```
        kubectl -n kube-system edit configmap eni-config
        ```

    2.  Paste the following content to the Terway ConfigMap and save the ConfigMap:

        ```
        "cilium_enable_hubble":"true",
        "cilium_hubble_listen_address":":4244",
        "cilium_hubble_metrics_server":":9091",
        "cilium_hubble_metrics":"drop,tcp,flow,port-distribution,icmp",
        ```

        |Parameter|Description|Remarks|
        |---------|-----------|-------|
        |eniip\_virtual\_type|Specifies whether to enable the IPvlan mode.|If the ConfigMap does not contain this parameter or the value is not set to `IPVLAN`, your cluster does not support ACK Tereway Hubble.|
        |cilium\_enable\_hubble|Specifies whether to enable ACK Terway Hubble to analyze network traffic.|This parameter is set to `"true"` in this example.|
        |cilium\_hubble\_listen\_address|Specifies the port that is used to expose the Hubble Service.|This parameter is set to `":4244"` in this example.|
        |cilium\_hubble\_metrics\_server|Specifies the port that is used to expose the Hubble metrics server.|This parameter is set to `":9091"` in this example.|
        |cilium\_hubble\_metrics|Separate the Hubble metrics with commas \(,\).|Layer 7 network capabilities such as HTTP and DNS are not supported. ACK Terway Hubble can collect the following metrics: `"drop,tcp,flow,port-distribution,icmp"`. **Note:** If you specify excessive metrics, the performance of ACK Terway Hubble may be degraded. |

3.  Restart the Terway pods for the modified ConfigMap to take effect.

    1.  Run the following command to query the Terway pods:

        ```
        kubectl -n kube-system get pod | grep terway-eniip
        ```

    2.  Run the following command to delete a Terway pod:

        ```
         kubectl -n kube-system delete pod terway-eniip-xxx
        ```

        Replace terway-eniip-xxx with the name of the Terway pod. Repeat the preceding steps to delete all Terway pods.


## Step 2: Install ACK Terway Hubble

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, choose **Marketplace** \> **App Catalog**.

3.  On the **App Catalog** page, search for ack-terway-hubble and click it.

4.  Select your cluster in the right-side **Deploy** panel.

5.  On the **App Catalog - ack-terway-hubble** page, click the **Parameters** tab. Set `hosts` under `ingress`. The host is used to log on to Hubble UI. You can set other parameters as needed.

    **Note:** The following parameters must be specified under `ingress`.

    |Parameter|Description|
    |---------|-----------|
    |enabled|Specifies whether to use the Ingress to access Hubble-UI.|
    |annotations|Specifies the annotations of the Ingress.|
    |path|Specifies the root path of the Ingress.|
    |hosts|Specifies the host of the Ingress.|
    |tls|Specifies the TLS settings of the Ingress.|

6.  In the right-side **Deploy** panel, click **Create**.


## Step 3: Get started with ACK Tereway Hubble

If you have set the `hosts` parameter for the Ingress of ACK Terway Hubble, you can log on to Hubble UI by accessing the Ingress host over port 80. When you access the Ingress host, the following information appears:

**Note:** If the domain that you access is not an authoritative domain, such as ingress.local, you must run the `kubectl -n kube-system get svc nginx-ingress-lb` command to query the IP address of Hubble UI. Then, modify the hosts file on your computer to map ingress.local to the IP address of Hubble UI.

-   In the upper portion of the page, you can view the topologies of pods and Services that belong to different namespaces.
-   In the lower portion of the page, you can view the sources, destinations, ports, and forwarding states of network traffic.
-   If you have configured network policies, you can view packets that are dropped because of network policy mismatching.

![Observability](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4800483261/p283630.png)

ACK Terway Hubble uses the hubble-metrics Service in the kube-system namespace to expose network flow metrics. You can specify the metrics that ACK Terway Hubble exposes by setting the `cilium_hubble_metrics` parameter in the Terway ConfigMap eni-config. You can use Prometheus Service and Application Real-Time Monitoring Service \(ARMS\) Prometheus to collect these metrics. For more information, see [Use Prometheus to monitor a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Use Prometheus to monitor a Kubernetes cluster.md) and [Enable ARMS Prometheus](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Monitoring management/Enable ARMS Prometheus.md).

**Note:** For more information about the metrics that ACK Terway Hubble can expose, see [hubble-exported-metrics](https://docs.cilium.io/en/v1.8/operations/metrics/#hubble-exported-metrics).

|Metric|Name|Label|Description|
|------|----|-----|-----------|
|drop|hubble\_drop\_total|reason, protocol|Number of dropped packets.|
|tcp|hubble\_tcp\_flags\_total|flag, family|TCP flag occurrences.|
|flow|hubble\_flows\_processed\_total|type, subtype, verdict|Total number of network flows processed.|
|port-distribution|hubble\_port\_distribution\_total|protocol, port|Numbers of packets distributed by destination port.|
|icmp|hubble\_icmp\_total|family, type|Number of ICMP messages|


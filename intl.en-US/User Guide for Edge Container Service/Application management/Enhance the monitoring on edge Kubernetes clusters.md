---
keyword: [metrics-server, edge monitoring, edge O&M tunnel]
---

# Enhance the monitoring on edge Kubernetes clusters

This topic describes how to integrate an additional operations and maintenance \(O&M\) and monitoring component with an edge Kubernetes cluster by using the edge O&M tunnel. This allows you to enhance the monitoring and seamlessly implement your O&M and monitoring system on applications in an edge Kubernetes cluster.

-   In a managed edge Kubernetes cluster, metrics-server is automatically installed to collect the metrics of containers by using kubelet. The metrics include the CPU and memory usage of containers.
-   When you migrate services to the cloud, you must also migrate the O&M and monitoring system to ensure a seamless migration. If you want to collect more metrics, such as that of service applications, you must integrate self-developed or open source monitoring components with the cluster.

**Note:**

To integrate an additional O&M and monitoring component with an edge Kubernetes cluster by using the O&M tunnel, make sure that the following requirements are met \(not applicable to other monitoring solutions\):

-   The O&M and monitoring component and the O&M tunnel component edge-tunnel-server are deployed on the same node in the cloud.
-   The O&M and monitoring component obtains the metrics of edge services by using **HTTP pull requests**.

## Deployment model

The following figure shows how to integrate an additional O&M and monitoring component with an edge Kubernetes cluster.

![Deployment model](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/1983068951/p100632.png)

## Configure an additional O&M and monitoring component

You must first configure the additional O&M and monitoring component based on the access mode that the component uses to monitor objects. Configure the additional component based on the following description:

-   Use the **node IP address:port** mode to obtain metrics

    The monitoring component uses http://node IP address:port/\{path\} as the endpoint to obtain metrics.

    -   Deploy applications in an edge Kubernetes cluster in the host network.
    -   By default, ports 10250 and 10255 are supported.
    -   For other ports, you must configure the edge-tunnel-server-cfg ConfigMap to integrate the additional O&M and monitoring component. The following example describes how to configure the ConfigMap.

        Assume that the monitoring component uses http://\{node IP address\}:8080/metrics as the endpoint to obtain the application metrics. Perform the following steps to configure the ConfigMap.

        Run the following command:

        ```
        cat <<EOF | kubectl apply -f
        ```

        The following output is returned:

        ```
        apiVersion: v1
        data:
          dnat-ports-pair: '8080=10264' #Access port 1: 10264. Access port 2: 10264.
        kind: ConfigMap
        metadata:
          name: edge-tunnel-server-cfg
          namespace: kube-system
        EOF
        ```

-   Use the **pod IP address:port** mode to obtain metrics

    The monitoring component uses http://pod IP address:port/\{path\} as the endpoint to obtain metrics.

    This mode is unavailable in current version.


**Related topics**  


[ACK@Edge overview](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md)

[Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md)


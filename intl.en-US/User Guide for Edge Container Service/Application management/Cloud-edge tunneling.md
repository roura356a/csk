---
keyword: [cloud-edge tunnel, managed edge Kubernetes clusters, access the edge from the cloud]
---

# Cloud-edge tunneling

By default, Container Service for Kubernetes \(ACK\) deploys the **edge-tunnel-server or edge-tunnel-agent** component after you create a cluster. This improves the user experience. These components are used to establish tunnels from the cloud to the edge. After the tunnels are established, ACK can proactively access edge nodes from the cloud to perform O&M operations. This topic describes the features of the components related to the cloud-edge tunnel and how to extend the monitoring of capabilities of edge nodes.

## Background

-   In a Kubernetes-native cluster, the controller components in the cloud must run commands and pass the maintenance requests to the kubelet on edge nodes. The monitoring component metrics-server must retrieve monitoring data of edge nodes from the cloud. If the edge nodes of a managed edge Kubernetes cluster are deployed in an internal network, you cannot directly access the edge nodes from the cloud.
-   The **edge-tunnel-server** component is deployed as a Deployment on nodes in the cloud. The **edge-tunnel-agent** component is deployed as a DaemonSet on each edge node.
-   When you perform O&M operations by running Kubernetes-native commands, such as kubectl logs and kubectl exec, or commands of metrics-server, the requests are sent to port 10250 and port 10255 of the kubelet on edge nodes.

## Features

-   When you create a managed edge Kubernetes cluster, you must create at least one Elastic Compute Service \(ECS\) instance to deploy the **edge-tunnel-server** component.
-   To establish secure and encrypted tunnels over the Internet, the system creates a Server Load Balancer \(SLB\) instance for the Service that is created by **edge-tunnel-server**. The **edge-tunnel-agent** component on each edge node establishes a tunnel to edge-tunnel-server through the SLB instance.
-   When components in the cloud, such as kube-apiserver and metrics-server, send requests to port 10250 and port 10255 of edge nodes, the requests are automatically forwarded to edge-tunnel-server, without the need to modify the components in the cloud.
-   The following figure shows how the cloud-edge tunneling works.

    ![G-11](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/6629146161/p225154.png)


**Note:**

-   When edge nodes are disconnected from the cloud or the network connection is weak, the tunnels may fail to work as normal.
-   If you delete or stop the SLB instance through which the tunnels are established, the tunnels cannot work as normal.
-   For edge Kubernetes clusters of early Kubernetes versions, such as 1.16.9-aliyunedge.1, the components in the cloud, such as metrics-server and edge-tunnel-server, must be deployed on the same ECS node. Otherwise, these components cannot work as normal. For edge Kubernetes clusters of 1.18.8-aliyunedge.1 or later, you can deploy metrics-server and edge-tunnel-server on different nodes.

## Configure access to ports other than 10250 and 10255

-   When you migrate your monitoring system to the cloud, access to ports other than 10250 and 10255 of edge nodes is required to ensure a seamless migration. For example, you can access port 9051 to collect the monitoring data.
-   You can enable components in the cloud to access ports other than 10250 and 10255 of edge nodes in the following ways:
    -   Modify the `dnat-ports-pair` field of the `edge-tunnel-server-cfg` ConfigMap in the kube-system namespace.
    -   Set `dnat-ports-pair` in the following format: `port 1=10264, port 2=10264`.

To enable components in the cloud to access port 9051 and port 9052 to collect monitoring data, use the following configuration:

```
cat <<EOF | kubectl apply -f
apiVersion: v1
data:
  dnat-ports-pair: '9051=10264,9052=10264'
kind: ConfigMap
metadata:
  name: edge-tunnel-server-cfg
  namespace: kube-system
EOF
```

**Note:** Monitoring data collected through ports other than 10250 and 10255 can be transmitted only over HTTP.


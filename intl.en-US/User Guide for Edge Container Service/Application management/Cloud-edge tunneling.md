---
keyword: [cloud-edge communication, managed edge Kubernetes clusters, cloud-edge tunneling]
---

# Cloud-edge tunneling

By default, Container Service for Kubernetes \(ACK\) deploys the **edge-tunnel-server or edge-tunnel-agent** component after you create a cluster. This improves user experience. These components are used to establish tunnels from the cloud to edge. After the tunnels are established, ACK can proactively access edge nodes from the cloud. This topic describes the tunneling components provided by managed edge Kubernetes clusters and the features of these components.

## Background information

-   In a Kubernetes-native cluster, the controller components in the cloud must run commands in Kubelet to manage and maintain edge nodes. The monitor components must retrieve monitoring data of edge nodes from other services in the cloud. If the edge nodes of a managed edge Kubernetes cluster are deployed in an internal network, you cannot directly access the edge nodes from the cloud.
-   The **edge-tunnel-server** component is deployed on cloud nodes as a Deployment. The **edge-tunnel-agent** component is deployed on each edge node as a DaemonSet.

## Features

-   When you create a managed edge Kubernetes cluster, you must create at least one Elastic Compute Service \(ECS\) instance to deploy the **edge-tunnel-server** component.
-   To establish secure and encrypted tunnels over the Internet, the system creates a Server Load Balancer \(SLB\) instance for the Service that is created by **edge-tunnel-server**. The **edge-tunnel-agent** component on each edge node establishes a tunnel to the SLB instance. The following figure shows how cloud-edge tunneling works.

    ![G-11](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6629146161/p225154.png)


**Note:**

-   When edge nodes are disconnected from the cloud or the network connection is weak, the tunnels may fail to function as normal.
-   If you delete or stop the SLB instance to which the tunnels are established, all of the tunnels are down.


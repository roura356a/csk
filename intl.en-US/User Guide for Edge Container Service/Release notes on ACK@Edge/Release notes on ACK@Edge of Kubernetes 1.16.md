---
keyword: [ACK@Edge, release notes]
---

# Release notes on ACK@Edge of Kubernetes 1.16

ACK@Edge is a cloud-managed solution that is provided by Container Service for Kubernetes \(ACK\) to implement collaborative cloud-edge computing. This topic lists the latest changes to ACK@Edge of Kubernetes 1.16.

## Kubernetes Core

The following describes the changes made to ACK@Edge of Kubernetes 1.16 in edge computing scenarios:

-   Fixes the issue that **kubelet** fails to start when more than four records are stored in the cpuacct.stat file of a node.
-   Kube-proxy supports the IP Virtual Server \(IPVS\) mode.
-   You can use **kubelet** to configure the internal IP address of a node by specifying the name of a network interface controller \(NIC\).

For more information about the release notes on ACK, see [Kubernetes 1.16 release notes](/intl.en-US/Release notes/Kubernetes release notes/Kubernetes 1.16 release notes.md).

## Autonomy of edge nodes

ACK@Edge of Kubernetes 1.16 enhances the stability of edge node autonomy. The following lists the major changes:

-   If the cached data is lost, clients receive the HTTP status code 404 instead of an empty string.
-   The directory that is used to store the certificate of **edge-hub** changes from /etc/kubernetes/edge-hub to /var/lib/edge-hub.
-   The certificate name of **edge-hub** changes from **edge-hub.kubeconfig** to **edge-hub.conf, bootstrap-edge-hub-current.conf --\> bootstrap-hub.conf**.
-   An interface is added for **prometheus metrics**.
-   The performance of **iptables** is improved. **iptables notrack** is added for IP addresses 127.0.0.1:10261 and 169.254.2.1:10261.

For more information, see [Autonomy of edge nodes]().

## Cloud-edge tunnels

ACK@Edge of Kubernetes 1.16 optimizes the performance of cloud-edge tunnels. The following lists the major changes:

-   The tunneling protocol changes from TCP to **gRPC**. Compared with TCP, the size of data transmitted over **gRPC** tunnels is reduced by 40%.
-   The **edge-tunnel-agent** component can automatically apply for and update certificates. This decouples the component from node certificates. In addition, the certificate of **edge-tunnel-agent** is stored in the /var/lib/edge-tunnel-agent/pki directory.
-   **prometheus metrics** are added.
-   The label that is used to deploy the pod for **edge-tunnel-agent** is changed to `alibabacloud.com/is-edge-worker: "true"`.

For more information, see [Cloud-edge tunneling](/intl.en-US/User Guide for Edge Container Service/Application management/Cloud-edge tunneling.md).

## Monitor components

ACK@Edge of Kubernetes 1.16 upgrades the **metrics-server** component and reinforces the security of this component. The following lists the major changes:

-   **metrics-server** is upgraded from V0.2.1 to V0.3.8.
-   ACK@Edge can be connected to Cloud Monitor by using tokens.

## Cell-based management at the edge

ACK@Edge of Kubernetes 1.16 provides a new component **yurt-app-manager** and supports cell-based management at the edge. The following describes the main features of cell-based management at the edge:

-   Manage nodes by node pool.
-   Manage applications by using the UnitedDeployment controller.
-   Configure a Service topology to expose a Service to only the node or node pool where the Service is deployed.

For more information about cell-based management at the edge, see [Overview of edge node pools](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Manage edge node pools/Overview of edge node pools.md).

## Enhanced node pools

Enhanced node pools are supported by ACK@Edge of Kubernetes 1.16. An enhanced node pool has the following features:

-   Allows you to establish more stable and secure tunnels between the cloud and enhanced node pool.
-   Allows applications in on-premises networks at the edge to communicate with applications in the cloud by using container networking.

For more information, see [Create an enhanced edge node pool \(public preview\)](/intl.en-US/User Guide for Edge Container Service/Cell-based management at the edge/Manage edge node pools/Create an enhanced edge node pool (public preview).md).

## Container runtimes

The following lists the major changes to container runtimes in ACK@Edge of Kubernetes 1.16:

-   The runC version of Advanced RISC Machine \(ARM\) and ARM64 is upgraded to 1.0.0-rc10.
-   Cgroupfs cgroup driver is changed to Systemd cgroup driver.

## CNI plug-in

ACK@Edge of Kubernetes 1.16 enhances the stability of the Container Network Interface \(CNI\) plug-in. This fixes the issue that pods with the same name in different namespaces may be allocated invalid IP addresses.

## Add edge nodes to a cluster

ACK@Edge of Kubernetes 1.16 optimizes the procedure of adding edge nodes to a cluster and adds some parameters. The following lists the major changes:

-   The procedure of adding edge nodes to a cluster is optimized and Classless Inter-Domain Routing \(CIDR\) conflict check is supported.
-   The number of IP addresses that can be assigned to nodes is configurable.
-   Parameters such as labels, nodeIface, annotations, and taints are added.
-   The Linux 5.4 kernel released by Ubuntu is supported.

For more information, see [Add an edge node](/intl.en-US/User Guide for Edge Container Service/Node management/Add an edge node.md).

## API changes

You can call the node pool API operations to manage edge node pools. For more information, see [Node pools](/intl.en-US/API Reference/Node pools/Create a node pool.md).


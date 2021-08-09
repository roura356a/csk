---
keyword: [network autonomy of edge nodes, edge computing]
---

# Network autonomy of edge nodes

The network autonomy of edge nodes ensures that applications are automatically reconnected to each other after they recover from exceptions. This topic describes the network autonomy of edge nodes.

## Background

By default, an edge node becomes network-autonomous after it is connected to a cluster in the cloud.

-   For a pod that runs on a network-autonomous edge node, the pod IP address is bound to the pod name. The IP address of the pod remains unchanged even if the application that runs on the pod is restarted, or the node to which the pod belongs is restarted. In addition, the name of the node is bound to the MAC address of VXLAN Tunnel Endpoint \(VTEP\). In this example, VTEP is the virtual network interface controller \(NIC\) flannel.1. The MAC address of VTEP remains unchanged even if containers that use Flannel are restarted, or the node is restarted.
-   Edge nodes may have exceptions or disconnect from the controllers in the cloud. In this case, if the edge nodes are network-autonomous, communication within an edge node or across edge nodes can be automatically restored after the applications or nodes are restarted. This applies to cross-node communication in edge computing scenarios where the network connection is weak.

## Features

-   By default, applications are deployed on network-autonomous nodes in both host network mode and non-host network mode.
-   The network autonomy of edge nodes ensures that applications are automatically reconnected to each other across nodes after the applications recover from exceptions. The following figure shows how network-autonomous edge nodes work.

![G-10](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3868896161/p225147.png)

**Note:** If a pod is recreated or migrated to another node, the IP address of the pod is changed.


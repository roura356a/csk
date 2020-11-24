# FAQ

This topic provides answers to some commonly asked questions about cluster management.

## Can I add nodes that support Intel Software Guard Extensions \(Intel SGX\) to an existing cluster?

To add nodes that support Intel SGX to an existing cluster, the cluster must meet the following conditions:

-   The Kubernetes version is 1.14.0 or later.
-   The network plug-in is **Flannel**.
-   The operating system is set to AliyunLinux 2.xxxx when you create the cluster. Do not select custom images when you add nodes to the cluster.

Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com/). On the Clusters page, find a cluster that meets the preceding requirements and choose **More** \> **Manage System Components** in the **Actions** column. On the Add-ons page, you can install Intel SGX Architectural Enclave Service Manager \(Intel SGX AESM\) and sgx-device-plugin.


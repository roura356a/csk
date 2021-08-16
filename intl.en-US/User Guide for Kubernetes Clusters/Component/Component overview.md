---
keyword: [cluster components, container service, ACK, components]
---

# Component overview

Container Service for Kubernetes \(ACK\) provides various types of components. You can install, upgrade, or uninstall components based on your business requirements. The topic lists the cluster components that are managed by ACK based on their features.

## Component types

The cluster components managed by ACK are classified into system components and optional components.

-   System components are automatically installed when you create ACK clusters.
-   Optional components extend the features of clusters. You can choose to install optional components when you create ACK clusters.

## Core components

|Component|Type|Description|
|---------|----|-----------|
|[Kube API Server](/intl.en-US/Release notes/System Component change Records/Core components/Kube API Server.md)|System component|The access gateway to a Kubernetes cluster.|
|[Kube Controller Manager](/intl.en-US/Release notes/System Component change Records/Core components/Kube Controller Manager.md)|System component|Manages the resources in a Kubernetes cluster.|
|[Cloud Controller Manager](/intl.en-US/Release notes/System Component change Records/Core components/Cloud Controller Manager.md)|System component|Enables integration between Kubernetes and Alibaba Cloud fundamentals, such as Classic Load Balancer \(CLB\) and Virtual Private Cloud \(VPC\).|

## Application management

|Component|Type|Description|
|---------|----|-----------|
|[appcenter](/intl.en-US/Release notes/System Component change Records/Application management/appcenter.md)|Optional component|Allows you to manage application deployments and lifecycles for multiple clusters in a centralized manner.|
|[progressive-delivery-tool](/intl.en-US/Release notes/System Component change Records/Application management/progressive-delivery-tool.md)|Optional component|Allows canary releases of applications in a progressive manner.|

## Logging and monitoring

|Component|Type|Description|
|---------|----|-----------|
|[alicloud-monitor-controller](/intl.en-US/Release notes/System Component change Records/Logs and monitoring/alicloud-monitor-controller.md)|System component|Enables integration with CloudMonitor.|
|[metrics-server](/intl.en-US/Release notes/System Component change Records/Logs and monitoring/metrics-server.md)|System component|This component is developed based on the open source component Metrics Server and can collect resource metrics. This component also provides the Metrics API for data consumption and supports Horizontal Pod Autoscaler \(HPA\).|
|[ack-node-problem-detector](/intl.en-US/Release notes/System Component change Records/Logs and monitoring/ack-node-problem-detector.md)|Optional component|This component is developed based on the open source component Node Problem Detector \(NPD\), and can monitor the health of nodes and connect to third-party monitoring platforms.|
|[ags-metrics-collector](/intl.en-US/Release notes/System Component change Records/Logs and monitoring/ags-metrics-collector.md)|Optional component|Allows Alibaba Cloud Genomics Service \(AGS\) users to monitor the resources that are used by each node in AGS workflows.|
|[ack-arms-prometheus]()|Optional component|Monitors ACK clusters by using Application Real-Time Monitoring Service \(ARMS\) Prometheus.|
|[logtail-ds](/intl.en-US/Data Collection/Logtail collection/Release notes.md)|Optional component|Collects container logs by using Log Service.|
|[logtail-windows](/intl.en-US/Release notes/System Component change Records/Logs and monitoring/logtail-windows.md)|Optional component|Collects log data from Windows containers and sends the data to Log Service.|

## Storage

|Component|Type|Description|
|---------|----|-----------|
|[csi-plugin](/intl.en-US/Release notes/System Component change Records/Storage/csi-plugin.md)|Optional component|Allows you to mount and unmount volumes. This component is automatically installed if you select the CSI plug-in when you create ACK clusters. |
|[csi-provisioner](/intl.en-US/Release notes/System Component change Records/Storage/csi-provisioner.md)|Optional component|Allows you to automate volume provisioning. This component is automatically installed if you select the CSI plug-in when you create ACK clusters. |
|[storage-operator](/intl.en-US/Release notes/System Component change Records/Storage/storage-operator.md)|Optional component|Manages the lifecycle of storage components.|
|[alicloud-disk-controller](/intl.en-US/Release notes/System Component change Records/Storage/alicloud-disk-controller.md)|Optional component|Allows you to automate the provisioning of disk volumes.|
|[FlexVolume](/intl.en-US/Release notes/System Component change Records/Storage/FlexVolume.md)|Optional component|An open source component developed at an early stage to enable volume expansion. The FlexVolume component is used to mount and unmount volumes. This component is automatically installed if you select the FlexVolume plug-in when you create ACK clusters. |

## Networking

|Component|Type|Description|
|---------|----|-----------|
|[CoreDNS](/intl.en-US/Release notes/System Component change Records/Networking/CoreDNS.md)|System component|The default component that is used to implement DNS-based service discovery in ACK clusters. This component follows the specifications of DNS-based service discovery in Kubernetes.|
|[Nginx Ingress Controller](/intl.en-US/Release notes/System Component change Records/Networking/Nginx Ingress Controller.md)|System component|Parses the routing rules of the Ingresses in ACK clusters. After an Ingress controller receives a request that matches a routing rule, the request is routed to the backend Service.|
|[managed-kube-proxy-windows](/intl.en-US/Release notes/System Component change Records/Other SQL statements/managed-kube-proxy-windows.md)|System component|A containerized kube-proxy used by managed Kubernetes clusters. This component manages the endpoints of Services on Windows nodes, including internal endpoints and external endpoints.|
|[Terway](/intl.en-US/Release notes/System Component change Records/Networking/Terway.md)|Optional component|An open source Container Network Interface \(CNI\) plug-in developed by Alibaba Cloud. This component is used together with VPC and allows you to use standard Kubernetes network policies to regulate how containers communicate with each other. Terway allows you to set up network connectivity within a Kubernetes cluster. This component is automatically installed if you select the Terway plug-in when you create ACK clusters. |
|[Flannel](/intl.en-US/Release notes/System Component change Records/Networking/Flannel.md)|Optional component|A CNI plug-in that allows you to create a virtual network for containers based on VPC. This component is automatically installed if you select the Flannel plug-in when you create ACK clusters. |
|[ACK NodeLocal DNSCache](/intl.en-US/Release notes/System Component change Records/Networking/ACK NodeLocal DNSCache.md)|Optional component|A local DNS caching solution developed based on the open source NodeLocal DNSCache project.|
|[kube-flannel-ds-windows](/intl.en-US/Release notes/System Component change Records/Networking/kube-flannel-ds-windows.md)|Optional component|A container network plug-in used in managed Kubernetes clusters to set up l2bridge networks that connect Windows containers.|

## Security

|Component|Type|Description|
|---------|----|-----------|
|[aliyun-acr-credential-helper](/intl.en-US/Release notes/System Component change Records/Security/aliyun-acr-credential-helper.md)|System component|Allows you to pull private images without passwords from instances of Container Registry Enterprise Edition and Personal Edition.|
|[gatekeeper](/intl.en-US/Release notes/System Component change Records/Security/gatekeeper.md)|Optional component|Helps you manage and enforce the policies executed by Open Policy Agent \(OPA\) in ACK clusters. Allows you to manage the labels of namespaces.|
|[kritis-validation-hook](/intl.en-US/Release notes/System Component change Records/Security/kritis-validation-hook.md)|Optional component|A key component that is used to verify image signatures.|
|[security-inspector](/intl.en-US/Release notes/System Component change Records/Security/security-inspector.md)|Optional component|A key component that is used to perform security inspections.|
|[ack-kubernetes-webhook-injector](/intl.en-US/Release notes/System Component change Records/Security/ack-kubernetes-webhook-injector.md)|Optional component|Allows you to dynamically add pod IP addresses to or remove pod IP addresses from the whitelists of various Alibaba Cloud services. This frees you from manual operations.|

## Other components

|Component|Type|Description|
|---------|----|-----------|
|[ack-arena](/intl.en-US/Release notes/System Component change Records/Other SQL statements/ack-arena.md)|Optional component|Simplifies the installation of the open source Arena tool. Allows you to install Arena in the ACK console in an efficient manner.|
|[ack-cost-exporter](/intl.en-US/Release notes/System Component change Records/Other SQL statements/ack-cost-exporter.md)|Optional component|Allows you to process the data generated from the cost analysis feature.|
|[t2095127.md\#]()|Optional component|Allows you to scale workloads based on a schedule.|
|[ack-virtual-node](/intl.en-US/Release notes/System Component change Records/Other SQL statements/ack-virtual-node.md)|Optional component|This component is developed based on the open source Virtual Kubelet project and adds support for Aliyun Provider. A lot of improvements are made to this component to enable seamless integration between Kubernetes and Elastic Container Instance.|
|[Intel SGX AESM](/intl.en-US/Release notes/System Component change Records/Other SQL statements/Intel SGX AESM.md)|Optional component|Intel \(R\) Software Guard Extensions \(SGX\) Architectural Enclave Service Manager \(AESM\) is a system component of Intel SGX. This component provides launch support for SGX Enclave, and services such as key provisioning and remote attestation.|
|[aliyun-acr-acceleration-suite](/intl.en-US/Release notes/System Component change Records/Other SQL statements/aliyun-acr-acceleration-suite.md)|Optional component|A client plug-in that enables on-demand image loading. This component is deployed as a DaemonSet on worker nodes.|
|[migrate-controller](/intl.en-US/Release notes/System Component change Records/Other SQL statements/migrate-controller.md)|Optional component|This component is developed based on the open source Velero project and allows you to migrate Kubernetes applications.|
|[resource-controller](/intl.en-US/Release notes/System Component change Records/Other SQL statements/resource-controller.md)|Optional component|A key component that is used to dynamically schedule pods. If you want to enable topology-aware CPU scheduling for professional Kubernetes clusters, this component is required.|
|[sandboxed-container-controller](/intl.en-US/Release notes/System Component change Records/Other SQL statements/sandboxed-container-controller.md)|Optional component|A controller component that is provided by the Sandboxed-Container runtime to enhance and extend the basic features of sandboxed containers.|
|[sandboxed-container-helper](/intl.en-US/Release notes/System Component change Records/Other SQL statements/sandboxed-container-helper.md)|Optional component|Allows you to perform health checks and O&M operations on sandboxed containers.|
|[sgx-device-plugin](/intl.en-US/Release notes/System Component change Records/Other SQL statements/sgx-device-plugin.md)|Optional component|A Kubernetes device plug-in developed by ACK and Ant Financial. This component simplifies the use of Intel \(R\) Software Guard Extensions \(SGX\) in containers.|
|[directx-device-plugin-windows](/intl.en-US/Release notes/System Component change Records/Other SQL statements/directx-device-plugin-windows.md)|Optional component|A DirectX device plug-in for ACK clusters.|

**Related topics**  


[Manage system components](/intl.en-US/User Guide for Kubernetes Clusters/Component/Manage system components.md)


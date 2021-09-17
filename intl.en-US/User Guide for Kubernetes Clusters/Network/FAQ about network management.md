# FAQ about network management

This topic provides answers to some frequently asked questions about container networks, Services, and Ingresses.

## FAQ about container networks

-   [How do I resolve the issue that the cluster installed with the Terway network plug-in cannot access the Internet after I create a new vSwitch for the cluster?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I resolve the issue that Flannel becomes incompatible with clusters of Kubernetes 1.16 or later after I manually upgrade Flannel?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I resolve the issue that a pod is not immediately ready for communication after it is started?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I enable a pod to access a Service that is exposed on the pod?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [Which network plug-in should I choose for an ACK cluster, Terway or Flannel?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I plan the network of a cluster?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [Can I use hostPorts to create port mappings in an ACK cluster?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [Can I configure multiple route tables for the VPC where my cluster is deployed?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I check the network type and vSwitches of a cluster?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I check the cloud resources used in an ACK cluster?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I resolve network errors of pods in the cluster?](~~142373~~)
-   [How do I obtain the public IP address of an application in the cluster?](~~142274~~)
-   [How do I resolve network errors that occur when the Terway network plug-in is used in exclusive ENI mode?](~~147426~~)
-   [How do I troubleshoot cluster connection issues?](~~149275~~)
-   [How do I resolve the issue that the IP addresses provided by the vSwitch are insufficient when the Terway network plug-in is used?](~~189784~~)
-   [How do I resolve the issue that the cluster cannot connect to the public IP address of the SLB instance that is associated with the LoadBalancer Service?](~~171437~~)

## Service FAQ

**FAQ about Server Load Balancer \(SLB\)**

-   [Why are no events collected during the synchronization between a Service and an SLB instance?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [How do I handle an SLB instance that remains in the Pending state?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [What do I do if the vServer groups of an SLB instance are not updated?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [What do I do if the annotations of a Service do not take effect?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [Why is the configuration of an SLB instance modified?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [Why does the cluster fail to access the IP address of the SLB instance?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [What do I do if I accidentally delete an SLB instance?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [If I delete a Service, is the SLB instance associated with the Service automatically deleted?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [How do I rename an SLB instance when the CCM version is V1.9.3.10 or earlier?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [How does CCM calculate node weights in Local mode?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)

**FAQ about Cloud Controller Manager \(CCM\) upgrades**

-   [How do I troubleshoot failures to upgrade CCM?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)

**FAQ about using existing SLB instances**

-   [Why does the system fail to use an existing SLB instance for more than one Services?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [Why is no listener created when I reuse an existing SLB instance?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)

**Other FAQ**

-   [How is session persistence implemented in Kubernetes Services?](~~149276~~)

## Ingress FAQ

-   [Which Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\) protocol versions are supported by Ingresses?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Do Ingresses pass Layer 7 request headers?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Can ingress-nginx forward requests to HTTPS Services at the backend?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Do Ingresses pass client IP addresses at Layer 7?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Does nginx-ingress-controller support HTTP Strict Transport Security \(HSTS\)?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Which rewrite rules are supported by ingress-nginx?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [How do I fix the issue that Log Service cannot parse logs as expected after ingress-nginx-controller is upgraded?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [t1860692.dita\#task\_2401024](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress controller to use an internal-facing SLB instance.md)
-   [What are the system updates after I upgrade the NGINX Ingress controller on the Add-ons page in the console?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [How do I configure ingress-nginx to use an existing SLB instance?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [How do I change Layer 4 listeners to Layer 7 listeners for ingress-nginx?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Why does the "certificate signed by unknown authority" error occur when I create an Ingress?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)


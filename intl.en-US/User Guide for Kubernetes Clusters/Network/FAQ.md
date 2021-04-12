# FAQ

This topic provides answers to some commonly asked questions about container networks, Services, and Ingresses.

## FAQ about container management

-   [How do I solve the issue that Flannel becomes incompatible with clusters of Kubernetes 1.16 or later after I manually upgrade Flannel?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I resolve the issue that a pod is not immediately ready for communication after it is started?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I enable a pod to access the Service that is exposed on it?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [Which network plug-in should I choose for an ACK cluster, Terway or Flannel?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [How do I plan the network of a cluster?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)
-   [Can I use hostPorts to create port mappings in an ACK cluster?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Container network/FAQ about container networks.md)

## Service FAQ

**FAQ about Server Load Balancer \(SLB\)**

-   [Why is the event of a Service synchronizing with the SLB instance not collected?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [How do I handle an SLB instance that remains in the Pending state?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [What do I do if the vServer groups of an SLB instance are not upgraded?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [What do I do if the annotations of a Service do not take effect?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [Why is the configuration of an SLB instance modified?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [Why does the cluster fail to access the IP address of the SLB instance?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [What do I do if I accidentally delete an SLB instance?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [Is the corresponding SLB instance deleted after I delete a Service?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [How do I rename an SLB instance when the CCM version is V1.9.3.10 or earlier?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [How does CCM calculate node weights in Local mode?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)

**FAQ about upgrading Cloud Controller Manager \(CCM\)**

-   [What do I do if CCM fails to upgrade?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)

**FAQ about using existing SLB instances**

-   [Why does the system fail to use an existing SLB instance for more than one Services?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)
-   [Why is no listener created when I reuse an existing SLB instance?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)

**Others**

-   [How do I connect CCM to a Kubernetes cluster that is created on top of Elastic Compute Service \(ECS\)?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Service Management/Service FAQ.md)

## Ingress FAQ

-   [Which Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\) protocol versions are supported by Ingresses?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Does Ingress pass Layer 7 request headers?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Can ingress-nginx forward requests to HTTPS Services at the backend?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Does Ingress pass client IP addresses at Layer 7?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Does nginx-ingress-controller support HTTP Strict Transport Security \(HSTS\)?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [Which rewrite rules are supported by ingress-nginx?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)
-   [How do I fix the issue that Log Service cannot parse logs as normal after ingress-nginx-controller is upgraded?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Ingress FAQ.md)


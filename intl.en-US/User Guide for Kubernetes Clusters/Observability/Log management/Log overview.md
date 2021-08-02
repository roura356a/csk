---
keyword: [log management, log overview]
---

# Log overview

The log of a Kubernetes cluster helps you troubleshoot the cluster and locate issues. This topic describes how to manage the log of applications by using Container Service for Kubernetes \(ACK\).

ACK provides various methods of application log management.

-   You can use Log Service to collect the log of applications in a convenient manner. This allows you to use various log analysis features provided by Log Service. For more information, see [Collect log files from containers by using Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect log files from containers by using Log Service.md).
-   You can configure a YAML file to export the log of an ACK cluster to Log Service without the need to modify the application. For more information, see [Configure Log4jAppender for Kubernetes and Log Service](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Configure Log4jAppender for Kubernetes and Log Service.md).
-   You can export the log of control plane components to a Log Service project that belongs to your account. For more information, see [Collect the logs of control plane components in a managed Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Collect the logs of control plane components in a managed Kubernetes cluster.md).
-   CoreDNS is deployed in ACK clusters to serve as a Domain Name System \(DNS\) server. You can check the log of CoreDNS to analyze the reasons why the DNS resolution is slow or resolution queries for high-risk domain names are received. For more information, see [Monitor and analyze the log of CoreDNS](/intl.en-US/User Guide for Kubernetes Clusters/Observability/Log management/Monitor and analyze the log of CoreDNS.md).


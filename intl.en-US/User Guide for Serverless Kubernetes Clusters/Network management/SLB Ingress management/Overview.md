---
keyword: SLB Ingress
---

# Overview

This topic introduces Ingresses and describes how Ingress controllers and Server Load Balancer \(SLB\) Ingress controllers work.

## Introduction to Ingress

In a Kubernetes cluster, an Ingress functions as an access point that exposes Services in the cluster. It distributes most of the network traffic that is destined for the Services in the cluster. An Ingress is a Kubernetes resource. It manages external access to the Services in a Kubernetes cluster. You can configure forwarding rules for an Ingress to route network traffic to backend pods of different Services.

## How an Ingress controller works

To ensure that the Ingress resource in a cluster works as expected, you must deploy an Ingress controller in the cluster to parse the Ingress rules. After an Ingress controller receives a request that matches an Ingress rule, the request is routed to the corresponding Service. Then, the Service forwards the request to pods and the pods process the request. In a Kubernetes cluster, Services, Ingresses, and Ingress controllers work in the following process:

-   A Service is an abstraction of an application that is deployed on a set of replicated pods.
-   An Ingress contains reverse proxy rules. It controls to which Services HTTP or HTTPS requests are routed. For example, an Ingress routes requests to different Services based on the hosts and URLs in the requests.
-   An Ingress controller is a reverse proxy program that parses Ingress rules. If changes are made to the Ingress rules, the Ingress controller updates the Ingress rules accordingly. After an Ingress controller receives a request, it redirects the request to a Service based on the Ingress rules.

The changes of Ingresses in a Kubernetes cluster are updated to Ingress controllers through the cluster API server. The configurations of SLB Ingress controllers are dynamically generated based on these changes. Then, Ingress rules are updated based on the configurations.

![How an Ingress controller works](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0511674161/p207789.png)

## How an SLB Ingress controller works

**Note:** SLB instances that serve Ingresses are fully managed by SLB Ingress controllers. To avoid service interruptions caused by Ingress errors, we recommend that you do not modify these SLB instances in the SLB console.

Serverless Kubernetes \(ASK\) clusters provide fully managed SLB Ingress controllers based on [Forward requests based on domain names or URLs](/intl.en-US/Tutorials/CLB/Forward requests based on domain names or URLs.md). This saves you the Infrastructure as a service \(IaaS\) cost and provides cloud service-level disaster recoveries for Ingresses.

-   If you do not specify an SLB instance, ASK automatically creates an Internet-facing SLB instance. ASK does not create an internal-facing SLB instance for an ASK cluster.
-   You can use existing Internet-facing or internal-facing SLB instances only by specifying instance IDs in annotations.
    -   If no existing Internet-facing instance is available, you can create one. Then, specify the ID of the newly created instance in annotations.
    -   If no existing internal-facing instance is available, you can create one. Then, specify the ID of the newly created instance in annotations.
-   If you use an existing SLB instance, it must be a high-performance instance that supports elastic network interfaces \(ENIs\). In addition, make sure that port 80 and port 443 are not used by other Services.
-   SLB instances listen on port 80 for HTTP requests and listen on port 443 for HTTPS requests.
-   SLB instances automatically use the certificate stored in the **Secret** that is specified in the configurations of the first Ingress created in the cluster. If no **Secret** is specified in the configurations of this Ingress, the default **fake** certificate is used.

**Related topics**  


[Overview](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Network management/NGINX Ingress management/Overview.md)


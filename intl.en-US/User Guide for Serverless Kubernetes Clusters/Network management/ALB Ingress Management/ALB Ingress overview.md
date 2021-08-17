---
keyword: ALB Ingress
---

# ALB Ingress overview

This topic introduces Ingresses and describes how Ingress controllers and Application Load Balancer \(ALB\) Ingress controllers work.

## Introduction to Ingresses

In a Kubernetes cluster, an Ingress functions as an access point that exposes Services in the cluster. It distributes most of the network traffic that is destined for the Services in the cluster. An Ingress is a Kubernetes resource. It manages external access to the Services in a Kubernetes cluster. You can configure forwarding rules for an Ingress to route network traffic to backend pods of different Services.

## How an Ingress controller works

To ensure that the Ingress resource in a cluster works as expected, you must deploy an Ingress controller in the cluster to parse the Ingress rules. After an Ingress controller receives a request that matches an Ingress rule, the request is routed to the corresponding Service. Then, the Service forwards the request to pods and the pods process the request. In a Kubernetes cluster, Services, Ingresses, and Ingress controllers work in the following process:

-   A Service is an abstraction of an application that is deployed on a set of replicated pods.
-   An Ingress contains reverse proxy rules. It controls to which Services HTTP or HTTPS requests are routed. For example, an Ingress routes requests to different Services based on the hosts and URLs in the requests.
-   An Ingress controller is a reverse proxy program that parses Ingress rules. If changes are made to the Ingress rules, the Ingress controller updates the Ingress rules accordingly. After an Ingress controller receives a request, it redirects the request to a Service based on the Ingress rules.

The changes to Ingresses in a Kubernetes cluster are updated to Ingress controllers through the cluster API server. The configurations of Server load Balancer \(SLB\) instances are dynamically generated based on these changes. Then, Ingress rules are updated based on the configurations.

![How an Ingress controller works](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0511674161/p207789.png)

## How an ALB Ingress controller works

**Note:** ALB instances that serve Ingresses are fully managed by ALB Ingress controllers. To avoid service interruptions caused by Ingress errors, we recommend that you do not modify these ALB instances in the ALB console.

ALB Ingresses are compatible with NGINX Ingresses and provide improved traffic management based on ALB instances. ALB Ingresses support complex routing, automatic certificate discovery, and the HTTP, HTTPS, and Quick UDP Internet Connection \(QUIC\) protocols. These features fully meet the requirements of cloud-native applications for ultra-high elasticity and balancing of heavy traffic loads at Lay 7.

**Related topics**  


[Access Services by using an ALB Ingress](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Network management/ALB Ingress Management/Access Services by using an ALB Ingress.md)

[Manage the ALB Ingress controller](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Network management/ALB Ingress Management/Manage the ALB Ingress controller.md)


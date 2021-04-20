---
keyword: Nginx Ingress
---

# Overview

This topic introduces Ingresses and describes how Ingress controllers work. It also introduces Ingresses in serverless Kubernetes \(ASK\) clusters.

## Introduction to Ingress

In a Kubernetes cluster, an Ingress functions as an access point that exposes Services in the cluster. It distributes most of the network traffic that is destined for the Services in the cluster. An Ingress is a Kubernetes resource. It manages external access to the Services in a Kubernetes cluster. You can configure forwarding rules for an Ingress to route network traffic to backend pods of different Services.

## How an Ingress controller works

To ensure that the Ingress resource in a cluster works as expected, you must deploy an Ingress controller in the cluster to parse the Ingress rules. After an Ingress controller receives a request that matches an Ingress rule, the request is routed to the corresponding Service. Then, the Service forwards the request to pods and the pods process the request. In a Kubernetes cluster, Services, Ingresses, and Ingress controllers work in the following process:

-   A Service is an abstraction of an application that is deployed on a set of replicated pods.
-   An Ingress contains reverse proxy rules. It controls to which Services HTTP or HTTPS requests are routed. For example, an Ingress routes requests to different Services based on the hosts and URLs in the requests.
-   An Ingress controller is a reverse proxy program that parses Ingress rules. If changes are made to the Ingress rules, the Ingress controller updates the Ingress rules accordingly. After an Ingress controller receives a request, it redirects the request to a Service based on the Ingress rules.

The changes of Ingresses in a Kubernetes cluster are updated to Ingress controllers through the cluster API server. The configurations of SLB Ingress controllers are dynamically generated based on these changes. Then, Ingress rules are updated based on the configurations.

![S2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5673888161/p241496.png)

## Ingresses in ASK clusters

ASK clusters provide NGINX Ingress controllers that are optimized on top of the open source version. You can choose to install an NGINX Ingress controller when you create an ASK cluster. You can also manually install it on the Add-ons page in the console after the cluster is created.

**Related topics**  


[Create an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md)

[Advanced Ingress usage](/intl.en-US/User Guide for Serverless Kubernetes Clusters/Network management/Nginx Ingress management/Advanced Ingress usage.md)

[Overview]()


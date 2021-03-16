# Overview

This topic describes what an Ingress is and how an Ingress controller works.

## What is an Ingress

In a Kubernetes cluster, an Ingress functions as an access point that exposes Services in the cluster. It distributes most of the network traffic that is destined for the Services in the cluster. An Ingress is a Kubernetes resource. It manages external access to the Services in a Kubernetes cluster. You can configure forwarding rules for an Ingress to route network traffic to backend pods of different Services.

Ingresses can only control HTTP traffic routing by rules. They do not support advanced features such as load balancing algorithms and session affinity. To enable these features, you must configure them on Ingress controllers.

## How an Ingress controller works

To ensure that the Ingress resource in a cluster works as expected, you must deploy an Ingress controller in the cluster to parse the Ingress rules. After an Ingress controller receives a request that matches an Ingress rule, the request is routed to the corresponding Service. Then, the Service forwards the request to pods and the pods process the request. In a Kubernetes cluster, Services, Ingresses, and Ingress controllers work in the following process:

-   A Service is an abstraction of an application that is deployed on a set of replicated pods.
-   An Ingress contains reverse proxy rules. It controls to which Services HTTP or HTTPS requests are routed. For example, an Ingress routes requests to different Services based on the hosts and URLs in the requests.
-   An Ingress controller is a reverse proxy program that parses Ingress rules. If changes are made to the Ingress rules, the Ingress controller updates the Ingress rules accordingly. After an Ingress controller receives a request, it redirects the request to a Service based on the Ingress rules.

Ingress controllers acquire Ingress rule changes from the API server and dynamically generate configuration files, such as nginx.conf. These configuration files are required by a load balancer, such as NGINX. Then, the Ingress controllers reload the load balancer. For example, the Ingress controllers run the `nginx -s load` command to reload NGINX and then generate new Ingress rules.

![How Services, Ingresses, and Ingress controllers work](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0511674161/p207789.png)

Ingress controllers can create Server Load Balancing \(SLB\) instances for Services of the LoadBalancer type. The SLB instances are used to expose Services in Kubernetes clusters. Ingress rules are then used to control to which Services requests are routed.

ingress-nginx is a Kubernetes-native Ingress controller. The ingress-nginx Ingress controller provided by Alibaba Cloud is optimized for Container Service for Kubernetes \(ACK\). You can install this ingress controller by selecting the ingress-nginx component when you create an ACK cluster.

**Related topics**  


[Configure an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Configure an Ingress.md)

[Basic operations of an Ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Basic operations of an Ingress.md)

[Monitor and analyze the logs of nginx-ingress](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/Monitor and analyze the logs of nginx-ingress.md)

[Use an Ingress controller to mirror network traffic](/intl.en-US/User Guide for Kubernetes Clusters/Network/Best practices/Use an Ingress controller to mirror network traffic.md)


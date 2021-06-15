---
keyword: NGINX Ingress controller
---

# NGINX Ingress controller

This topic introduces Ingresses and describes how Ingresses work. It also lists the latest changes to the NGINX Ingress controller.

## Introduction to Ingress

In a Kubernetes cluster, an Ingress functions as an access point that exposes Services in the cluster. It distributes most of the network traffic that is destined for the Services in the cluster. An Ingress is a Kubernetes resource. It manages external access to the Services in a Kubernetes cluster. You can configure forwarding rules for an Ingress to route network traffic to backend pods of different Services.

## How an Ingress controller works

To ensure that the Ingress resource in a cluster works as expected, you must deploy an Ingress controller in the cluster to parse the Ingress rules. After an Ingress controller receives a request that matches an Ingress rule, the request is routed to the corresponding Service. Then, the Service forwards the request to pods and the pods process the request. In a Kubernetes cluster, Services, Ingresses, and Ingress controllers work in the following process:

-   A Service is an abstraction of an application that is deployed on a set of replicated pods.
-   An Ingress contains reverse proxy rules. It controls to which Services HTTP or HTTPS requests are routed. For example, an Ingress routes requests to different Services based on the hosts and URLs in the requests.
-   An Ingress controller is a reverse proxy program that parses Ingress rules. If changes are made to the Ingress rules, the Ingress controller updates the Ingress rules accordingly. After an Ingress controller receives a request, it redirects the request to a Service based on the Ingress rules.

The changes of Ingresses in a Kubernetes cluster are updated to Ingress controllers through the cluster API server. The configurations of SLB Ingress controllers are dynamically generated based on these changes. Then, Ingress rules are updated based on the configurations.

![S2](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5673888161/p241496.png)

## June 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.44.0.3-8e83e7dc6-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.44.0.3-8e83e7dc6-aliyun|2021-06-01|Vulnerability CVE-2021-23017 is fixed. For more information, see [Updating NGINX for a DNS Resolver Vulnerability \(CVE-2021-23017\)](https://www.nginx.com/blog/updating-nginx-dns-resolver-vulnerability-cve-2021-23017/).|The upgrade may temporarily interrupt your service. We recommend that you upgrade the NGINX Ingress controller during off-peak hours.|

## April 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.44.0.2-abf1c6fe4-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.44.0.2-abf1c6fe4-aliyun|2021-04-01|Compatibility with the `the_real_ip` field in the log\_format parameter of NGINX Ingress controller V0.30 or earlier versions is added.|The upgrade may temporarily interrupt your service. We recommend that you upgrade the NGINX Ingress controller during off-peak hours.|

## March 2021

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.44.0.1-5e842447b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.44.0.1-5e842447b-aliyun|2021-03-08|-   Validating admission webhooks are enabled by default. For more information, see [How the NGINX Ingress controller works](https://kubernetes.github.io/ingress-nginx/how-it-works/#avoiding-outage-from-wrong-configuration).
-   The validity of the `service-weight` annotation is checked.
-   The performance of persistent connections and short-lived connections is increased by 20% to 50%.
-   OCSP stapling is supported.
-   LuaJIT is upgraded to V2.1.0.
-   NGINX is upgraded to V1.19.6.
-   Alpine Linux is upgraded to V3.13 for base images.
-   CVE-identified vulnerabilities related to OpenSSL are fixed.
-   TLS 1.3 is enabled by default.

**Note:** By default, only TLS 1.2 and TLS 1.3 are supported. For more information about how to enable support for TLS 1.0 and TLS 1.1, see [Which Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\) protocol versions are supported by Ingresses?](/intl.en-US/User Guide for Kubernetes Clusters/Network/Ingress management/FAQ about Ingresses.md).

-   The Kubernetes version must be V1.16 or later.
-   The NGINX Ingress controller is upgraded based on Ingress-NGINX 0.44.0. For more information, see [Ingress-NGINX changelog](https://github.com/kubernetes/ingress-nginx/blob/master/Changelog.md).

|The upgrade may temporarily interrupt your service. We recommend that you upgrade Ingress-NGINX during off-peak hours.|

## April 2020

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.30.0.1-5f89cb606-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.30.0.1-5f89cb606-aliyun|2020-04-02|-   FastCGI Backend is supported.
-   The Dynamic SSL Cert Update mode is enabled by default.
-   Traffic mirroring is supported.
-   NGINX is upgraded to V1.17.8 and OpenResty is upgraded to V1.15.8. The operating system of base images is updated to Alpine Linux.
-   Ingress validating admission webhooks are supported.
-   The following vulnerabilities are fixed: CVE-2018-16843, CVE-2018-16844, CVE-2019-9511, CVE-2019-9513, and CVE-2019-9516.
-   Major updates:
    -   The lua-resty-waf, session-cookie-hash, and force-namespace-isolation configurations are deprecated.
    -   The data type of x-forwarded-prefix is changed from BOOLEAN to STRING.
    -   The the\_real\_ip field in log-format will be deprecated in the next version and replaced with remote\_addr.
-   The NGINX Ingress controller is upgraded based on Ingress-NGINX 0.30.0. For more information about the updates, see [Ingress-NGINX changelog](https://github.com/kubernetes/ingress-nginx/blob/master/Changelog.md).

|The upgrade may temporarily interrupt your service. We recommend that you upgrade the NGINX Ingress controller during off-peak hours.|

## October 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.22.0.5-552e0db-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.5-552e0db-aliyun|2019-10-24|Wildcard domain names, whitelists, and rewrite rules are supported if you enable dynamic update for NGINX upstream servers.|The upgrade may temporarily interrupt your service. We recommend that you upgrade the NGINX Ingress controller during off-peak hours.|

## July 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.22.0.4-5a14d4b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.4-5a14d4b-aliyun|2019-07-18|Canary release rules are optimized and the Perl regular expressions are supported.|The upgrade may temporarily interrupt your service. We recommend that you upgrade the NGINX Ingress controller during off-peak hours.|

## April 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.22.0.3-da10b7f-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.22.0.3-da10b7f-aliyun|2019-04-25|-   The NGINX Ingress controller is upgraded based on open source Ingress-NGINX 0.22.0. For more information about the updates, see [Ingress-NGINX](https://github.com/kubernetes/ingress-nginx/releases).
-   Blue-green releases and canary releases are supported if you enable dynamic update for NGINX upstream servers.
-   Dynamic update is enabled for NGINX upstream servers by default.
-   Major updates: Capture groups are used for rewrite-target annotations. For more information, see [rewrite-target](https://kubernetes.github.io/ingress-nginx/examples/rewrite/#rewrite-target). For more information about how to smoothly upgrade Ingress-NGINX, visit [GitHub](https://github.com/kubernetes/ingress-nginx/pull/3174#issuecomment-455665710).

|The upgrade may temporarily interrupt your service. We recommend that you upgrade the NGINX Ingress controller during off-peak hours.|

## January 2019

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.20.0.2-cc39f1b-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.20.0.2-cc39f1b-aliyun|2019-01-17|-   The default number of NGINX worker processes is limited. This avoids the issue that excess NGINX processes occupy host resources.
-   The port numbers of Services that route traffic to the old application version and the new application version can be different during blue-green releases and canary releases.
-   The NGINX configuration verification failure is fixed when no pod is active in the backend servers of the new application version during canary releases.
-   The issue that an Ingress address endpoint is not updated due to failed connections to the Kubernetes API server is fixed.

|The upgrade may temporarily interrupt your service. We recommend that you upgrade the NGINX Ingress controller during off-peak hours.|

## November 2018

|Version|Image address|Release date|Description|Impact|
|-------|-------------|------------|-----------|------|
|v0.20.0.1-4597ce2-aliyun|registry.cn-hangzhou.aliyuncs.com/acs/aliyun-ingress-controller:v0.20.0.1-4597ce2-aliyun|2018-11-29|-   The NGINX Ingress controller is upgraded based on open source Ingress-NGINX 0.20.0. For more information about the updates, see [Ingress-NGINX](https://github.com/kubernetes/ingress-nginx/releases).
-   NGINX is upgraded to V1.15.6 and HTTP/2-related vulnerabilities are fixed.
-   Regular expressions are supported in the path parameter.
-   The default-http-backend Service is removed and custom default backend Services are supported.
-   Blacklists based on IP addresses, user agents, and referer headers are supported.
-   The default permissions are optimized and the privileged permissions are removed.
-   Apache JServ Protocol \(AJP\) is supported.

|The upgrade may temporarily interrupt your service. We recommend that you upgrade the NGINX Ingress controller during off-peak hours.|


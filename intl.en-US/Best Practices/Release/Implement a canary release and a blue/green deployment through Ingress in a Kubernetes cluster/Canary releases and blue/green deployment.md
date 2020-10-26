# Canary releases and blue/green deployment

This topic describes how to implement a canary release and a blue/green deployment by using the Ingress function provided by Alibaba Cloud Container Service for Kubernetes.

## Background information

With a canary release or a blue/green deployment, you can create two identical production environments for the latest version of the target software and an earlier version. Then you can apply specific rules to reroute traffic from the earlier version to the latest version without affecting the software of the earlier version. After the software of the latest version has run without exceptions for a specified period, you can reroute all traffic from the earlier version to the latest version.

A/B testing is a type of comparative and incremental canary release. Specifically, with A/B testing, you can keep some users using the service of an earlier version, and reroute traffic of other users to the service of the latest version. If the service of the latest version runs without exceptions for the specified period of time, then you can gradually reroute all user traffic to the service of the latest version.

## Scenarios

**Scenario 1**

For example, assume that Service A already runs online to provide an externally accessible Layer-7 service, and a new version of this service with new features, namely, Service A’, is developed. You want to release Service A’, but you do not want it to directly replace Service A at once. Additionally, you want the client requests of which the request headers contain `foo=bar` or the cookies contain `foo=bar` to be forwarded to Service A’. Then, after Service A’ has run without exceptions for a specified period, you want to reroute all traffic from Service A to Service A’, and then smoothly bring Service A offline.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7455359951/p9950.png)

**Scenario 2**

For example, assume that an earlier version of a service, named Service B, is running online to provide an externally accessible Layer-7 service. However, it has known problems. A new version, namely Service B' is developed with the problems fixed and you want to release this latest version. However, you initially want to reroute only 20% of all client traffic to Service B’. Then, after Service B’ has run without exceptions for a period, you want to reroute all traffic from Service B to Service B’, and then smoothly bring Service B offline.

![](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7455359951/p9951.png)

To meet the preceding application release requirements, Alibaba Cloud Container Service for Kubernetes uses the Ingress function to provide the following four methods of traffic distribution:

In A/B testing

-   Distribute traffic according to the request header
-   Distribute traffic according to the cookie
-   Distribute traffic according to the Query Param

In a blue/green deployment

-   Distribute traffic according to the service weight


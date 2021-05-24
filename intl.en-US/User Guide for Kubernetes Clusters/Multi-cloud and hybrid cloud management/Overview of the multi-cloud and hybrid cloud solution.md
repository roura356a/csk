---
keyword: [ACK, hybrid cloud]
---

# Overview of the multi-cloud and hybrid cloud solution

This topic describes the background and features of the multi-cloud and hybrid cloud solution provided by Container Service for Kubernetes \(ACK\).

## Background

Cloud hosting is the trend of the future. However, some enterprise customers opt for multi-cloud or hybrid cloud solutions out of concern about data sovereignty and security. The differences in infrastructure capabilities and security architectures among different cloud environments can lead to barriers between the enterprise IT architecture and operations and maintenance \(O&M\) systems. This increases the complexity of multi-cloud or hybrid cloud implementations and O&M costs.

In the cloud-native era, Kubernetes-led technologies shield the differences between infrastructures and usher in the arrival of application-centric multi-cloud and hybrid cloud architectures. This facilitates application lifecycle management and resource scheduling in a unified method in multi-cloud and hybrid cloud environments.

## Features

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8365359951/p90913.png)

The multi-cloud and hybrid cloud solution built on ACK and Alibaba Cloud Service Mesh \(ASM\) provides the following features:

-   Unified cluster management

    External Kubernetes clusters deployed in data centers or other clouds can be registered to the ACK console for unified management. The ACK console supports features such as security management, application management and monitoring, and logging in a unified method.

-   Unified scheduling and auto scaling

    Supports auto scaling based on a unified resource scheduling framework. This allows customers to make full use of computing resources and enables auto scaling to handle unexpected traffic spikes.

-   Unified service governance

    Supports nearby access, workload failover, and canary release based on ASM. This enables applications deployed among multiple clusters and across regions to support scenarios such as cloud disaster recovery and active geo-redundancy.


## Security features

When you register external Kubernetes clusters, you can use the ACK Register Agent component to manage these clusters. This component also allows you to deploy applications in external Kubernetes clusters and manage the lifecycle of these applications.

**Components**

|Component|Description|
|---------|-----------|
|ACK Console|The ACK console for cluster and service management.|
|ACK Register Agent|ACK Register Agent is an agent that runs on Deployments within an external Kubernetes cluster. ACK Register Agent receives requests from ACK Stub and forwards them to the Kubernetes API server. ACK Register Agent also receives responses from the API server and forwards them to ACK Stub.|
|ACK Stub|ACK Stub is a cluster registration proxy deployed on Alibaba Cloud. ACK launches ACK Stub for all external Kubernetes clusters registered in the ACK console. ACK Stub forwards requests between the ACK console and ACK Register Agent in external Kubernetes clusters.|
|K8s API Server|The API server runs in an external Kubernetes cluster.|

**Architecture of component connections**

In an architecture where ACK Stub and ACK Register Agent are used, requests from the ACK console are sent to the API server in an external Kubernetes cluster. ACK Register Agent runs on Deployments that create two pods within the external Kubernetes cluster. ACK Register Agent is connected to ACK Stub that is deployed in the ACK console. The following figure shows how ACK Register Agent forwards requests to the API server within an external Kubernetes cluster.

![Architecture of component connections](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128607.png)

After you register an external Kubernetes cluster in the ACK console, ACK Register Agent is deployed in the external Kubernetes cluster. Then, a two-way persistent connection is established between ACK Stub and ACK Register Agent over Transport Layer Security \(TLS\) 1.2. Requests from authorized users or ACK management services are first sent to ACK Stub through the TLS connection, then forwarded to ACK Register Agent, and finally delivered to the API server. After the API server receives the requests, the API server first performs authentication, authorization check, and admission control, then audits the requests, and finally returns responses. Responses are returned through the same TLS connection. They pass through ACK Register Agent and ACK Stub, and finally reach the ACK console. All requests sent to the external Kubernetes cluster through the connection are authenticated and verified. This ensures that the external Kubernetes cluster is accessed in a secure way.

**Security of intercommunication among components**

Authentication and authorization check is required for intercommunication among all components. All components to be accessed must pass security checks. This ensures that data is transmitted among only trusted components.

![Architecture of authentication and authorization check for components](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128690.png)

Authentication is based on the credentials of Resource Access Management \(RAM\) users and two-way TLS certification. Data is encrypted by TLS during transmission. Authorization check is based on RAM and the TLS \(x509\) certificate whitelisting.

-   Security of request transmission

    All credentials contained in requests that are sent through the TLS connection carry the identity information of the users who send the requests. The user identity information includes the credential issued by ACK to access an external cluster and the internal credential required to access ACK components. This ensures that all requests sent to API servers are verified and audited.

-   Security of user request transmission

    All user requests sent to external Kubernetes clusters must carry credentials issued by the ACK console. The credentials contain the user identity information. The following figure shows how a request with a credential is forwarded to an external cluster.

    ![Security of user request transmission](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128695.png)

    Cluster administrators on Alibaba Cloud can use RAM permission policies to control access from users to external clusters. Authorized RAM users can obtain credentials that are required to access external clusters from the ACK console. The credentials are provided to ACK Stub and ACK Register Agent for authentication. Data transmission among components is encrypted by TLS. After ACK Register Agent verifies the credential, the user identity carried in the credential is encapsulated into the impersonation headers of the request for the destination API server to authenticate the request. The API server performs authentication, authorization check, and admission control based on the received credential and user identity, and then audits the request.

-   Security of service request transmission

    Requests from ACK and ASM to the external API server are transmitted in the same way as user requests. The ACK console sends a request that carries the credential required for ACK Register Agent to authenticate the request. Then, the request is authenticated by ACK Stub and ACK Register Agent and forwarded to the destination API server. During this process, ACK Register Agent impersonates the user identity in the request and forwards the request to the API server over Layer 7. Finally, the API server performs authorization checks with Role-Based Access Control \(RBAC\) and then audits the request. Data transmission is encrypted by TLS from end to end.

    ![Security of service request transmission](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128705.png)


**Internal security of clusters**

The following figure shows how a request from ACK Register Agent is processed by an API server.

![Requests from ACK Register Agent](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128707.png)

After the API server receives the request, the API server first checks whether the credential of the request is valid.

-   If the credential is invalid, the API server returns a 401 error, which indicates that the request failed the authentication.
-   If the request passes the authentication, the API server checks whether the request contains valid impersonation headers. If the request contains valid impersonation headers, the request is passed to the next round with the impersonated user identity.

Then, the API server checks whether the impersonated user identity is granted the required permissions on the cluster.

-   If the request fails the authorization check, the API server returns a 403 error, which indicates that the user identity is unauthorized.
-   If the request passes the authorization check, the API server returns a response after it audits the request.

ACK Register Agent is a forward proxy and cluster registration component written in the Go programming language. The coding and publishing of ACK Register Agent are audited by Alibaba Cloud to ensure security. The administrators of registered external clusters must ensure the security of cluster nodes by following the best practices for Kubernetes security. They can ensure the security of ACK Register Agent and external clusters by using security configurations related to infrastructure and applications.


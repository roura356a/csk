---
keyword: [K8s, multi-cloud and hybrid cloud, best security practices]
---

# Security of multi-cloud and hybrid cloud deployment

ACK and service mesh provide solutions for multi-cloud and hybrid cloud deployment. These solutions offer the capabilities of cluster management, elastic scaling, and service governance. When you register external Kubernetes clusters, you can use ACK Register Agent to manage these clusters. This component also allows you to deploy applications in external Kubernetes clusters and manage the lifecycle of these applications. This topic provides a best practice for ensuring the security of external cluster management and registration when you use Container Service for Kubernetes \(ACK\) and service mesh to build a multi-cloud or hybrid cloud environment.

## Components

|Component|Description|
|---------|-----------|
|ACK Console|The ACK console for cluster and service management.|
|ACK Register Agent|ACK Register Agent is an agent that runs on Deployments within an external Kubernetes cluster. ACK Register Agent receives requests from ACK Stub and forwards them to the Kubernetes API Server. ACK Register Agent also receives responses from the API Server and forwards them to ACK Stub.|
|ACK Stub|ACK Stub is a cluster registration proxy deployed on Alibaba Cloud. ACK launches ACK Stub for all external Kubernetes clusters registered in the ACK console. ACK Stub forwards requests between the ACK console and ACK Register Agent in external Kubernetes clusters.|
|K8s API Server|The API Server runs in an external Kubernetes cluster.|

## Architecture of connections

In an architecture where ACK Stub and ACK Register Agent are used, requests from the ACK console are sent to the API Server in an external Kubernetes cluster. ACK Register Agent runs on Deployments that create two pod replicas within the external Kubernetes cluster. ACK Register Agent is connected to ACK Stub that is deployed in the ACK console. The following figure shows how ACK Register Agent forwards requests to the API Server within an external Kubernetes cluster.

![Architecture of connections](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128607.png)

After you register an external Kubernetes cluster in the ACK console, ACK Register Agent is deployed in the external Kubernetes cluster. Then, a two-way persistent connection is established between ACK Stub and ACK Register Agent over Transport Layer Security \(TLS\) 1.2. Requests from authorized users or ACK management services are first sent to ACK Stub through the TLS connection, and then forwarded to ACK Register Agent, and finally delivered to the API Server. After the API Server receives the requests, the API Server first performs authentication, authorization check, and admission control, then audits the requests, and finally returns responses. Responses are returned through the same TLS connection. They pass through ACK Register Agent and ACK Stub, and finally reach the ACK console. All requests sent to the external Kubernetes cluster through the connection are authenticated and verified. This ensures that the external Kubernetes cluster is accessed in a secure way.

## Security of intercommunication among components

Authentication and authorization check are required for intercommunication among all components. All components to be accessed must pass security check. This ensures that data is transmitted among only trusted components.

![Architecture of authentication and authorization check for components](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128690.png)

Authentication is based on the credentials of Resource Access Management \(RAM\) users and two-way TLS certification. Data is encrypted by TLS during transmission. Authorization check is based on RAM and the TLS \(x509\) certificate whitelisting.

## Security of request transmission

All credentials contained in requests that are sent through the TLS connection carry the identity information of the users that send the requests. The user identity information includes the credential issued by ACK to access an external cluster and the internal credential required to access ACK components. This ensures that all requests sent to the API Server are authenticated and audited.

**Security of user request transmission**

All user requests sent to external Kubernetes clusters must carry credentials issued by the ACK console. The credentials contain the user identity information. The following figure shows how a request with a credential is forwarded to an external cluster.

![Security of user request transmission](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128695.png)

Cluster administrators on Alibaba Cloud can use permission policies of RAM to control access from users to external clusters. Authorized RAM users can obtain credentials that are required to access external clusters from the ACK console. The credentials are provided to ACK Stub and ACK Register Agent for authentication. Data transmission among components is encrypted by TLS. After ACK Register Agent verifies the credential, the user identity carried in the credential is encapsulated into the impersonation headers of the request for the destination API Server to authenticate the request. The API Server performs authentication, authorization check, and admission control based on the received credential and user identity, and then audits the request.

**Security of service request transmission**

Requests from ACK and service mesh to the external API Server are transmitted in the same way as user requests. The ACK console sends a request that carries the credential required for ACK Register Agent to authenticate the request. Then, the request is authenticated by ACK Stub and ACK Register Agent and forwarded to the destination API Server. During this process, ACK Register Agent impersonates the user identity in the request and forwards the request to the API Sever over Layer 7. Finally, the API Server performs authentication with Role-Based Access Control \(RBAC\) and then audits the request. Data transmission is encrypted by TLS from end to end.

![Security of service request transmission](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128705.png)

## Internal security of clusters

The following figure shows how a request from ACK Register Agent is processed by an API Server.

![Requests from ACK Register Agent](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/4196767061/p128707.png)

After the API Server receives the request, the API Server first checks whether the credential of the request is valid.

-   If the credential is invalid, the API Server returns a 401 error, which indicates that the request failed the authentication.
-   If the request passes the authentication, the API Server checks whether the request contains valid impersonation headers. If the request contains valid impersonation headers, the request is passed to the next round with the impersonated user identity.

Then, the API Server checks whether the impersonated user identity is granted the required permissions on the cluster.

-   If the request fails the authorization check, the API Server returns a 403 error, which indicates that the user identity is not authorized.
-   If the request passes the authorization check, the API Server returns a response after it audits the request.

ACK Register Agent is a forward proxy and cluster registration component written in the Go programming language. The coding and publishing of ACK Register Agent are audited by Alibaba Cloud to ensure security. The administrators of registered external clusters must ensure the security of cluster nodes by following the best practices for Kubernetes security. They can ensure the security of ACK Register Agent and external clusters by using security configurations related to the infrastructure and applications.


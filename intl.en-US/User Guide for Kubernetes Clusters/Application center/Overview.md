---
keyword: [application center, GitOps, multiple clusters]
---

# Overview

Application center is a declarative, GitOps continuous delivery tool for Kubernetes based on Argo CD. In GitOps pattern, application definitions, configurations are declarative and version controlled, application deployment and lifecycle management are automated and easy to understand. This topic describes the features, architecture, concepts, and core components of **Application Center**.

## Features

The application center follows the GitOps pattern of using Git repositories as the source of truth for definning the desired application state. It continuously monitors running applications and compares the current live state against the desired target state\(specified in the Git repo\). Application center reports & visualizes the differences, while providing facilities to automatically or manually sync the live state back to the desired target state. Any modifications made to the desired target state in the Git repo can be automatically applied and reflected in the specified target environments.

Application center features list as below:

-   Automated deployment of applications to specified target clusters from Git repo or Helm OCI artifact repo.
-   Ability to manage and deploy to multiple clusters\(public cloud by ACK, edge computing, on-premises and other clouds\).
-   Health status analysis of application resources.
-   Automated or manual syncing of applications to its desired state.
-   Rollback/Roll-anywhere to any application configuration committed in Git repository.
-   Web UI which provides real-time view of application activity.
-   Webhook integration \(Git, ACREE\).

## Architecture

The application center enables centralized management of applications. This allows you to view the overall topologies of applications in your cluster. Application Center is applicable to the following scenarios:

-   You can view the deployment status and changes of all Kubernetes resources used by each application.
-   You can also use templates or Helm charts on GitHub to deploy different versions of an application to a cluster.
-   You can roll back the application to each version or deploy the application versions to a cluster.

![Architecture](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7855359951/p127895.png)

## Concepts

|Concept|Description|
|-------|-----------|
|Application|A set of ACK resources created based on an orchestration template.|
|Target state|The target state of an application is defined by files in repositories such as Gits or Helm charts.|
|Current state|The up-to-date state of an application, such as the states of all pods.|
|Deployment status|Whether the up-to-date state of an application is the same as the target state. Whether the state of a deployed application is the same as that defined by the Gits or Helm charts.|
|Deployment|The process of changing an application from the current state to a target state. For example, it may refer to the process where you perform kubectl apply to deploy an application in an ACK cluster.|
|Refresh|Compare the up-to-date state with the state as defined in the latest code in Gits.|
|Health condition|Whether the application is running properly.|

## Core components

All components of Application Center are deployed in the **appcenter** namespace of your cluster. The following table lists the four core components.

|Component|Description|
|---------|-----------|
|application-controller|Records and backtracks application versions and rolls back an application to a specified version.|
|redis|Caches application data and records the cached application data.|
|repo-server|Pulls deployment templates from remote repositories, such as Gits or Helm charts.|
|server|Makes a gRPC server accessible over the Internet. The gRPC server is used to receive and process external requests.|

**Related topics**  


[Deploy an application in Application Center](/intl.en-US/User Guide for Kubernetes Clusters/Application center/Application management/Deploy an application in Application Center.md)


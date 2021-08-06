---
keyword: [Application Center, deploy applications, application rollback, features of Application Center]
---

# Application Center overview

You can use **Application Center** in the Container Service for Kubernetes \(ACK\) console to deploy applications. This topic describes the concept, core components, and features of **Application Center**.

## Background

After you deploy an application by using an orchestration template, the resources of the application are distributed to different sections of the ACK console. You cannot view all Kubernetes resources of the application in a unified manner. In Kubernetes, an application may consist of several Kubernetes subresources. For example, a WordPress application contains resources such as Deployments, Services, Ingresses, service accounts, and roles. In earlier versions of the ACK console, after an application is deployed, you cannot gain a unified view of the application topology. Therefore, version management and rollback cannot be unified for continuous deployments.

Application Center provides a unified portal for your applications. This helps you understand how your applications are deployed in a unified view. Application Center allows you to view your applications in a global manner. You can also view the deployment status and changes of all Kubernetes subresources of each application. In addition, Git and Helm charts are used to deploy applications in ACK clusters based on application versions. This allows you to publish and roll back applications that are deployed in ACK clusters.

## Applicable scenarios

![Architecture](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/7855359951/p127895.png)

Application Center is applicable to the following scenarios:

-   Support cost-effective disaster recovery based on multiple clusters and release iterations with one click.
-   Deploy and synchronize workloads across regions on a global scale, including regions available on the China site \(aliyun.com\) and International site \(alibabacloud.com\)
-   Release the same application on the cloud, on-premises, and on a hybrid cloud.
-   View applications and manage workloads in a unified manner.
-   Use the same orchestration template to deploy applications in multiple environments. This significantly reduces the error rate.

## Terms

|Term|Description|
|----|-----------|
|Application|A set of ACK resources defined in an orchestration template.|
|Desired status|The desired status of an application is defined by the Helm charts or configurations files in Git.|
|Current status|The current status of an application, such as the status of all pods.|
|Deployment status|Whether the current status of an application is the same as the desired status. Whether the status of a deployed application is the same as that defined by the Helm charts or configurations files in Git.|
|Deployment|The process of changing an application from the current status to a desired status. For example, it may refer to the process where you run the kubectl apply command to deploy an application to an ACK cluster.|
|Refresh|Compare the current status with the status as defined by the latest code in Git.|
|Health status|Whether the application runs as normal.|

## Core component

All components of Application Center are deployed in the **appcenter** namespace of your cluster. The following table lists the core components.

|Component|Description|
|---------|-----------|
|application-controller|Records and backtracks application versions, and rolls back an application to a specified version|
|redis|Caches application data and records the cached application data|
|repo-server|Pulls orchestration templates from remote repositories, such as Git repositories or Helm repositories|
|server|Exposes a gRPC server to the Internet. The gRPC server is used to receive and process external requests.|

**Related topics**  


[t2067143.md\#]()

[t2066790.md\#]()

[t2067077.md\#]()


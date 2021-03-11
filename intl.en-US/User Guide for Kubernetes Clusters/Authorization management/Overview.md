# Overview

This topic describes the authorization mechanisms that are used to control the access to Container Service for Kubernetes \(ACK\) clusters and how to authorize RAM users to access resources in an ACK cluster.

You can authorize a user by using Resource Access Management \(RAM\) and role-based access control \(RBAC\).

## Attach a RAM policy

RAM policies control the access to the management APIs of a cluster. If you want to manage the visibility of a cluster or scale a cluster, [attach a RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md) to a RAM user. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).

## Assign an RBAC role

You can use the RBAC mechanism to grant users the permission to call the API server of an ACK cluster. For more information, see [Authorization overview](https://kubernetes.io/zh/docs/reference/access-authn-authz/authorization/). You can assign the following default roles to a RAM user in ACK:

|Role|RBAC-based permissions on one cluster|
|----|-------------------------------------|
|Administrator|The read and write permissions on resources in all namespaces.|
|O&M engineer|The read and write permissions on visible resources in all namespaces and the read-only permission on nodes, persistent volumes \(PVs\), namespaces, and quotas.|
|Developer|The read and write permissions on visible resources in one or all namespaces.|
|Restricted user|The read-only permission on visible resources in one or all namespaces.|
|Custom role|Different cluster roles have different permissions. Before you grant a role to a RAM user, make sure that all permissions of the selected role are required by the RAM user.|

**Note:** Before you use RBAC to authorize a RAM user, attach a RAM policy to the RAM user to define the read and write permissions on the ACK cluster.

## Procedure

![Authorization process map](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7135359951/p49552.png)

If you do not have a RAM user, create one. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md). Before you use a RAM user to log on to the ACK console, perform the following operations:

1.  Configure a RAM policy. For more information, see [Create a custom RAM policy](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Create a custom RAM policy.md).
2.  Configure user permissions based on the RBAC model. For more information, see [Assign RBAC roles to a RAM user](/intl.en-US/User Guide for Kubernetes Clusters/Authorization management/Assign RBAC roles to a RAM user.md).
3.  Log on to the [Container Service for Kubernetes \(ACK\) console](https://cs.console.aliyun.com) as a RAM user.

    If you have assigned roles to the Alibaba Cloud account, you can log on the ACK console to perform operations as a RAM user. Otherwise, assign a role to the RAM user first. For more information, see [Quick start for first-time users](/intl.en-US/Quick Start/Quick start for first-time users.md).



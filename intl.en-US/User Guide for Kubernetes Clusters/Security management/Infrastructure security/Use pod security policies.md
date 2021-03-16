---
keyword: [create or update a pod security policy, admission controller resource, psp]
---

# Use pod security policies

A pod security policy is an admission controller resource that validates requests to create and update pods in your cluster based on the rules that are defined by the policy. If a request to create or update a pod does not meet the rules, the request is rejected and an error is returned. This topic describes how to use pod security polices in a Container Service for Kubernetes \(ACK\) cluster.

Before you configure network policies, make sure that you have performed the following steps:

-   [An ACK cluster is created.](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a managed Kubernetes cluster.md)
-   [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Use kubectl to connect to an ACK cluster.md).

## The default pod security policy

By default, pod security policy control is enabled for standard managed Kubernetes clusters \(Kubernetes 1.16.6\) and standard dedicated Kubernetes clusters \(Kubernetes 1.16.6\). A pod security policy named ack.privileged is automatically created. This security policy accepts all types of pods. This provides the same effect as when pod security policy control is disabled for the cluster.

## Delete the ClusterRoleBinding resource that is related to the default pod security policy

**Warning:** Before you delete the ClusterRoleBinding resource, you must configure a custom pod security policy and a related RBAC binding. Otherwise, all users, controllers, and service accounts will be unable to create or update pods.

After you configure a custom pod security policy and a related RBAC binding, you can delete the ClusterRoleBinding resource of the default pod security policy ack.privileged to enable the custom pod security policy.

**Note:** Do not delete or rename ack.privileged and the ack:podsecuritypolicy:privileged ClusterRole. These two resources are required to run the cluster.

## Configure or restore the default pod security policy

**Related topics**  


[Pod Security Policies](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)


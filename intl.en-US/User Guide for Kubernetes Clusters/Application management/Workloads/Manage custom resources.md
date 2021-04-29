---
keyword: [resource objects, workloads, resource types, custom resources]
---

# Manage custom resources

Container Service for Kubernetes \(ACK\) clusters allow you to extend the Kubernetes API by adding custom resources. You can use custom resource definitions \(CRDs\) to define custom resources. You can view all API groups and resource types in your cluster. You can also view and manage resource objects of each resource type. This topic describes how to manage custom resources.

## Limits

For some resource types, only operations such as create are supported. The resource objects of these resource types are not listed.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Workloads** \> **Custom Resources**.

5.  Manage resource objects.

    -   View API groups and resource types.

        On the Resource Objects tab, all API groups supported by the cluster are listed on the left side of the page. You can click the name of an API group to view all resource types that are contained in the group. You can also enter a keyword in the search box to search for resource types.

    -   View resource objects.

        You can select a resource type in an API group to view the resource objects of the resource type. You can modify the YAML files of resource objects and delete resource objects.



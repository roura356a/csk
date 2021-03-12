---
keyword: [Object Browser, workloads, resource type, custom resource]
---

# Object Browser

Container Service for Kubernetes \(ACK\) clusters allow you to extend the Kubernetes API by using a custom resource definition \(CRD\) file. This requires that you add custom resources to the Kubernetes API. You can use the Object Explorer to view all API groups and the resource types that are managed in the API groups. You can also view the list of objects for each resource type and manage the objects. This topic describes how to use the Object Browser.

## Limits

For some resource types, only operations such as create are supported. The resource objects of these types cannot be listed.

## Procedure

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane, click **Object Explorer**.

5.  Manage the Object Browser.

    -   View API groups and resource types.

        The API groups supported by the cluster are listed in the Object Browser management pane. You can click an API group name to view all resource types that are managed in the group. You can also enter a keyword in the search bar to search for resource types.

    -   View resource objects.

        You can click a resource type in an API group to view the resource objects of the resource type. You can edit each resource object in the YAML format or delete the resource object.



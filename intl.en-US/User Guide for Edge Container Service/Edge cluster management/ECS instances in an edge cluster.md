---
keyword: [cloud management node, edge cluster, create ECS instances]
---

# ECS instances in an edge cluster

A managed Kubernetes cluster at the edge must contain at least one Elastic Compute Service \(ECS\) instance. This topic describes how ECS instances work and how to add or remove ECS instances.

## Cloud management nodes in edge computing

![Cloud management nodes in edge computing](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6883068951/p100700.png)

When you create a managed edge cluster, ACK@Edge automatically creates at least one ECS instance for the cluster and connects the ECS instance to the management nodes in the cloud. The ECS instance is used to deploy cloud management applications. You can deploy custom cloud management applications. By default, the **node-role.alibabacloud.com/addon: Effect: NoSchedule** taint is attached to the nodes in the cloud. This ensures that edge services are not deployed on the management nodes. By default, a management node of version 1.14.8-aliyunedge.1 or earlier has the following management applications:

-   alibaba-log-controller: the Log Service controller.
-   alicloud-monitor-controller: the Cloud Monitor controller.
-   metric-server: the server that is used to monitor the cluster.
-   edge-tunnel-server: the server for the reverse O&M tunnel. It allows you to use the native Kubernetes API to access edge nodes, monitor containers, and use SSH to remotely run commands.

## Deploy applications to management nodes in the cloud

If you want to deploy custom management applications on management nodes in the cloud, for example, to deploy different types of operators, you must specify a toleration that matches the preceding taint and configure NodeSelector. The following example describes how to set the parameters.

```
     ...
      nodeSelector:
        alibabacloud.com/is-edge-worker: 'false'
        beta.kubernetes.io/arch: amd64
        beta.kubernetes.io/os: linux
      tolerations:
        - effect: NoSchedule
          key: node-role.alibabacloud.com/addon
          operator: Exists
      ...
```

## Add a cloud management node

To add a cloud management node, you can perform the following steps. ACK@Edge will support ECS-based auto scaling in later versions.

1.  Purchase an ECS instance in the VPC where the cluster is located.

    For more information about how to purchase an instance, see [t9601.md\#]().

    **Note:** Set the operating system to CentOS 7.6.

2.  In [OpenAPI Explorer](https://api.alibabacloud.com/#/?product=Ons), call the AttachInstances operation to add the ECS instance to the cluster.

    The following example shows a request body:

    ```
    {
      "password": "Helloxxxx!",
      "tags":[],
      "instances": [
        "i-uf65mbpn1x8xxxxxx"
      ]
    }
    ```

    |Parameter|Description|
    |---------|-----------|
    |password|The password that is used to log on to the ECS instance. The password must be 8 to 30 characters in length and must contain at least three of the following types: uppercase letters, lowercase letters, digits, and special characters.|
    |tags|The tags of the nodes.    -   key: the name of the tag
    -   value: the value of the tag. |
    |instances|An array of the existing instances.|

    For more information about the API operation, see [Add existing ECS instances to a cluster](/intl.en-US/API Reference/Nodes/Add existing ECS instances to a cluster.md).


**Related topics**  


[ACK@Edge overview](/intl.en-US/User Guide for Edge Container Service/ACK@Edge overview.md)

[Create a managed edge Kubernetes cluster](/intl.en-US/User Guide for Edge Container Service/Edge cluster management/Create a managed edge Kubernetes cluster.md)


---
keyword: [reserved instances, Elastic Container Instance-based pod]
---

# Use reserved instances

When you use an elastic container instance to provide long-term services, you can use reserved instances to offset the cost of the elastic container instance. This topic describes how to use reserved instances.

## Prerequisites

1.  Reserved instances are purchased based on your business requirements. For more information, see [Purchase reserved instances](/intl.en-US/Instance/Instance purchasing options/Reserved Instances/Purchase reserved instances.md).

2.  Reserved instances are checked and managed. For more information, see [Split a reserved instance](/intl.en-US/Instance/Instance purchasing options/Reserved Instances/Split a reserved instance.md).


**Note:** Reserved instances are automatically applied to pay-as-you-go elastic container instances based on match rules. For more information about match rules, see [Match between reserved instances and pay-as-you-go instances](/intl.en-US/Instance/Instance purchasing options/Reserved Instances/Match between reserved instances and pay-as-you-go instances.md).

## How to use reserved instances

A reserved instance is applicable only when you create an elastic container instance based on a specified Elastic Compute Service \(ECS\) instance type. For more information, see [Create an ECI from a specified ECS instance type](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod/Create an ECI from a specified ECS instance type.md).

Add the following `annotation` to the `template` field of pod configurations. In this example, the reserved instance is used to purchase an `ecs.c5.large` instance.

```
annotations:
    k8s.aliyun.com/eci-instance-type : "ecs.c5.large"  # Replace the value with the actual ECS instance type in the following format: ecs.instance family.instance specification. For example, ecs.c6.3xlarge.
```

**Note:** `annotations` must be added to the `spec` field of pod configurations. Specify the ECS instance type based on your requirements. For more information about the supported ECS instance types, see [Instance families](/intl.en-US/Instance/Instance families.md).

Deployment example

`annotations` must be added to the `metadata` field of pod configurations.

**Note:** The zone of the reserved instance must be the same as that of your cluster. Otherwise, the reserved instance cannot match the elastic container instance created in your serverless Kubernetes \(ASK\) cluster.

```
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      annotations:
        k8s.aliyun.com/eci-instance-type : "ecs.c5.large"  # Replace the value with the actual ECS instance type based on your requirements.
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

## View the billing and application details about a reserved instance

1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Instances & Images** \> **Reserved Instances**.

3.  In the top navigation bar, select a region.

4.  Find the reserved instance that you want to view and click **View Bill** in the **Actions** column.

5.  On the **Manage Reserved Instances** page, click the **Details** tab.

    You can view the usage details of the reserved instance. These details show the ECS instance or elastic container instance whose fees are offset by the reserved instance during every billing hour.

    **Note:** One computing unit equals one vCPU. The applicable duration \(hours\) equals the number of computing units multiplied by the number of hours.



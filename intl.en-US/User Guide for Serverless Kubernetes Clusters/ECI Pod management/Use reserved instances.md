---
keyword: [reserved instances, ECI]
---

# Use reserved instances

When you use an Elastic Container Instance \(ECI\) to provide long-term services, you can use reserved instances to deduct the costs of the ECI. This topic describes how to use reserved instances.

## Preparations

1.  Purchase reserved instances based on your needs. For more information, see [Purchase reserved instances](/intl.en-US/Instance/Instance purchasing options/Reserved Instances/Purchase reserved instances.md).

2.  View and manage your reserved instances. For more information, see [Split a reserved instance](/intl.en-US/Instance/Instance purchasing options/Reserved Instances/Split a reserved instance.md).


**Note:** Reserved instances are automatically applied to pay-as-you-go ECIs based on matching rules. For more information about matching rules, see [Match between reserved instances and pay-as-you-go instances](/intl.en-US/Instance/Instance purchasing options/Reserved Instances/Match between reserved instances and pay-as-you-go instances.md).

## Usage

A reserved instance is only applicable when you create an ECI based on a specified ECS instance type. For more information, see [Create an ECI from a specified ECS instance type](/intl.en-US/User Guide for Serverless Kubernetes Clusters/ECI Pod management/Create an ECI from a specified ECS instance type.md).

Add the following annotations to the `templte` field of a pod. In this example, the reserved instance is used to purchase an ecs.c5.large instance.

```
annotations:
    k8s.aliyun.com/eci-instance-type : "ecs.c5.large"  #Replace the value with the ECS instance type in the following format: ecs. instance family. instance specification. For example, ecs.c6.3xlarge.
```

**Note:** `annotations` must be added to the `spec` field of the pod. Enter the ECS instance type as needed. For more information about supported ECS instance types, see [Instance families](/intl.en-US/Instance/Instance families.md).

Deployment example

`annotations` must be added to the `metadata` field of the pod.

**Note:** The zone of the reserved instance must be the same as that of your cluster. Otherwise, the reserved instance cannot match the ECI created in your serverless Kubernetes \(ASK\) cluster.

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
        k8s.aliyun.com/eci-instance-type : "ecs.c5.large"  #Replace the value with the ECS instance type as needed.
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

## View billing and deduction details about a reserved instance

View the bill of a reserved instance

1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Instances & Images** \> **Reserved Instances**.

3.  In the top navigation bar, select a region.

4.  Find the reserved instance that you want to view and click **View Bill** in the **Actions** column.

5.  On the **Manage Reserved Instances** page, click the **Details** tab.

    You can view the usage details of the reserved instance. These details show the ECS instance or ECI whose fees are deducted by the reserved instance during every billing hour.

    **Note:** One computing unit equals one vCPU. The deductible duration \(hours\) equals the number of computing units multiplied by the number of hours.



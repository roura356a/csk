---
keyword: [node pool scalability, node pool scaling out]
---

# Check the scalability of a node pool

The scale-out of a node pool may fail due to reasons such as insufficient instance inventory, insufficient zone options, or low Elastic Compute Service \(ECS\) configurations. If the number of available vSwitches decreases, the number of instances that can be added to the node pool is reduced. This reduces the scalability of the node pool. You can evaluate the scalability of your node pools and adjust the node resources in your cluster accordingly. This topic describes how to check the scalability of a node pool.

## Features

ACK calls the DescribeAvailableResource operation of ECS to query the available instances and their inventory availability in a specified region. The StatusCategory parameter in the response indicates the inventory availability of instance types in different zones. The results are used to evaluate the scalability of node pools. For more information, see [DescribeAvailableResource](/intl.en-US/API Reference/Regions/DescribeAvailableResource.md).

|Status|StatusCategory|Description|
|------|--------------|-----------|
|Available: indicates that the instance type is available for purchase.|WithStock: indicates that the instance type is in sufficient stock. You can prioritize this instance type in scale-out events.|The instance type is in stock and will be replenished.|
|ClosedWithStock: indicates that the instance type is in stock but will not be replenished.|The instance type is in stock but will not be replenished.|
|SoldOut: indicates that the instance type is sold out.|WithoutStock: indicates that the instance type is sold out but will be replenished.|The instance type is out of stock but will be replenished. The instance type is temporarily out of stock.|
|ClosedWithoutStock: indicates that the instance type is sold out and will not be replenished.|The instance type is out of stock and will not be replenished. If instances used by other users are released, the state of StatusCategory will be changed to ClosedWithStock.|

The scalability is evaluated based on the number of available resource pools. The number of available resource pools is calculated based on the number of resource pools in the WithStock state and the number of resource pools in the ClosedWithStock state. The weight of a resource pool in the WithStock state is 1. The weight of a resource pool in the ClosedWithStock state is 0.5.

Number of available resource pools = Number of resource pools in the WithStock state × 1 + Number of resource pools in the ClosedWithStock state × 0.5

-   If the number of available resource pools is fewer than two, the scalability is evaluated as low.
-   If the number of available resource pools is two or more but fewer than four, the scalability is evaluated as medium.
-   If the number of available resource pools is four or more, the scalability is evaluated as high.

## Example of evaluating node pool scalability

1.  Call DescribeAvailableResource in OpenAPI Explorer to query available instance types and their inventory availability in a specified region.

    For more information about how to call DescribeAvailableResource in OpenAPI Explorer, see [DescribeAvailableResource](https://api.aliyun.com/#product=Ecs&api=DescribeAvailableResource&type=RPC&version=2014-05-26).

    Sample request:

    ```
    https://ecs.aliyuncs.com/?Action=DescribeAvailableResource
    &RegionId=cn-beijing
    &DestinationResource=InstanceType
    &<Common request parameters>
    ```

    Sample response:

    ```
    {
        "RequestId": "735E42EB-197F-5331-8C83-68ECA68D****",
        "AvailableZones": {
            "AvailableZone": [
                {
                    "Status": "Available",
                    "StatusCategory": "WithStock",
                    "ZoneId": "cn-beijing-k",
                    "AvailableResources": {
                        "AvailableResource": [
                            {
                                "Type": "InstanceType",
                                "SupportedResources": {
                                    "SupportedResource": [
                                        {
                                            "Status": "Available",
                                            "StatusCategory": "WithStock",
                                            "Value": "ecs.s6-c1m2.small"
                                        },
                                        {
                                            "Status": "Available",
                                            "StatusCategory": "WithStock",
                                            "Value": "ecs.hfr7.xlarge"
                                        },
                                        {
                                            "Status": "Available",
                                            "StatusCategory": "ClosedWithoutStock",
                                            "Value": "ecs.sn1ne.2xlarge"
                                        },                                    
    {
                                            "Status": "Available",
                                            "StatusCategory": "ClosedWithStock",
                                            "Value": "ecs.hfg6.2xlarge"
                                        }
                                    ]
                                }
                            }
                        ]
                    },
                    "RegionId": "cn-beijing"
                },
                {
                    "Status": "Available",
                    "StatusCategory": "WithStock",
                    "ZoneId": "cn-beijing-h",
                    "AvailableResources": {
                        "AvailableResource": [
                            {
                                "Type": "InstanceType",
                                "SupportedResources": {
                                    "SupportedResource": [
                                        {
                                            "Status": "Available",
                                            "StatusCategory": "WithStock",
                                            "Value": "ecs.s6-c1m2.small"
                                        },
                                        {
                                            "Status": "Available",
                                            "StatusCategory": "WithStock",
                                            "Value": "ecs.n4.small"
                                        },
                                        {
                                            "Status": "Available",
                                            "StatusCategory": "WithoutStock",
                                            "Value": "ecs.video-trans.26xhevc"
                                        },                                    
                                                                            {
                                            "Status": "Available",
                                            "StatusCategory": "ClosedWithStock",
                                            "Value": "ecs.r6.2xlarge"
                                        },
                                    ]
                                }
                            }
                        ]
                    },
                    "RegionId": "cn-beijing"
                }
            ]
        }
    }
    ```

    The sample response indicates that the number of resource pools in the WithStock state is four and the number of resource pools in the ClosedWithStock state is two. The number of unavailable resource pools is two, including one resource pool in the WithoutStock state and one in the ClosedWithoutStock state.

2.  You can then calculate the number of available resource pools.

    The number of available resource pools is five, which indicates a high scalability.


## Check the scalability of a node pool

1.  Log on to the [ACK console](https://cs.console.aliyun.com).

2.  In the left-side navigation pane of the ACK console, click **Clusters**.

3.  On the Clusters page, find the cluster that you want to manage and click the name of the cluster or click **Details** in the **Actions** column. The details page of the cluster appears.

4.  In the left-side navigation pane of the details page, choose **Nodes** \> **Node Pools**.

5.  On the Node pools page, find the node pool that you want to check and click the name or click **Details** in the **Actions** column.

6.  On the node pool details page, click the Overview tab. In the **Node Pool Information** section, you can view the scalability to the right side of the **scaling group** ID.

    ![Scalability](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0007450361/p302865.png)


If the scalability is low, we recommend that you perform the following operations:

-   Specify vSwitches in multiple zones for the node pool.
-   Specify instance types that are in sufficient stock for the node pool.

For more information, see [t1880229.md\#section\_prc\_o3n\_q1d](/intl.en-US/User Guide for Kubernetes Clusters/Node management/Node pool management/Manage node pools.md).


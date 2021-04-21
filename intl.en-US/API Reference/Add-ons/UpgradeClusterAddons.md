# UpgradeClusterAddons

Upgrades a specified component of a Container Service for Kubernetes \(ACK\) cluster to the required version.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UpgradeClusterAddons&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/components/upgrade HTTP/1.1 
Content-Type:application/json
[ {
  "component_name" : "String",
  "next_version" : "String",
  "version" : "String"
} ]
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cf4299b79b3e34226abfdc80a4bda\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
| |Array|No| |The request parameters. |
|component\_name|String|Yes|coredns|The name of the component that you want to upgrade. |
|next\_version|String|Yes|1.6.7|The version to which the component can be upgraded. You can call `DescribeClusterAddonsVersion` to query the version to which the component can be upgraded. |
|version|String|Yes|v1.6.2|The current version of the component. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
POST /clusters/cf4299b79b3e34226abfdc80a4bda****/components/upgrade HTTP/1.1 
Content-Type:application/json
[ {
  "component_name" : "coredns",
  "next_version" : "1.6.7",
  "version" : "v1.6.2"
} ]
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


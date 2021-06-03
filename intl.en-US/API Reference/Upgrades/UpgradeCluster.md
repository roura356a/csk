# UpgradeCluster

Upgrades a Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=UpgradeCluster&type=ROA&version=2015-12-15)

## Request syntax

```
POST /api/v2/clusters/ClusterId/upgrade HTTP/1.1 
Content-Type:application/json
{
  "component_name" : "String",
  "next_version" : "String",
  "version" : "String"
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the ACK cluster that you want to upgrade. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|component\_name|String|No|k8s|The name of the component. Valid value: `k8s`. |
|next\_version|String|No|1.16.9-aliyun.1|The Kubernetes version to which you want to upgrade. |
|version|String|No|1.14.8-aliyun.1|The current Kubernetes version of the ACK cluster. For more information, see [Kubernetes versions](~~185269~~). |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
POST /api/v2/clusters/c82e6987e2961451182edacd74faf****/upgrade HTTP/1.1 
Content-Type:application/json
{
  "component_name" : "k8s",
  "next_version" : "1.16.9-aliyun.1",
  "version" : "1.14.8-aliyun.1"
}
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


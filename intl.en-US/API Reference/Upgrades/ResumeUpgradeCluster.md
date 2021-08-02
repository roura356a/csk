# ResumeUpgradeCluster

Resumes the upgrade of a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ResumeUpgradeCluster&type=ROA&version=2015-12-15)

## Request syntax

```
POST /api/v2/clusters/ClusterId/upgrade/resume HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the ACK cluster. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
POST /api/v2/clusters/c82e6987e2961451182edacd74faf****/upgrade/resume HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


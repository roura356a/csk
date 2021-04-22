# MigrateCluster

Migrates workloads from a standard managed cluster to a professional managed cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=MigrateCluster&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/cluster_id/migrate HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|cluster\_id|String|Yes|c21e0591548ba4c10bdb535d6572b\*\*\*\*|The ID of the cluster. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
POST /clusters/c21e0591548ba4c10bdb535d6572b****/migrate HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


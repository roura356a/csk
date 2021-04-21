# DescribeUserQuota

Queries resource quotas.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeUserQuota&type=ROA&version=2015-12-15)

## Request syntax

```
GET /quota HTTP/1.1 
Content-Type: application/json
Common request parameters
```

## Request parameters

None

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "amk_cluster_quota" : Long,
  "ask_cluster_quota" : Long,
  "cluster_nodepool_quota" : Long,
  "cluster_quota" : Long,
  "node_quota" : Long
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|amk\_cluster\_quota|Long|20|The quota of managed Kubernetes clusters. Default value: 20. To increase the quota, [submit a ticket](https://selfservice.console.aliyun.com/ticket/category/cs/today). |
|ask\_cluster\_quota|Long|3|The quota of serverless Kubernetes \(ASK\) clusters. Default value: 20. To increase the quota, [submit a ticket](https://selfservice.console.aliyun.com/ticket/category/cs/today). |
|cluster\_nodepool\_quota|Long|10|The quota of node pools in an ACK cluster. Default value: 20. To increase the quota, [submit a ticket](https://selfservice.console.aliyun.com/ticket/category/cs/today). |
|cluster\_quota|Long|50|The quota of clusters within the current account. Default value: 50. To increase the quota, [submit a ticket](https://selfservice.console.aliyun.com/ticket/category/cs/today). |
|node\_quota|Long|100|The quota of nodes in an ACK cluster. Default value: 100. To increase the quota, [submit a ticket](https://selfservice.console.aliyun.com/ticket/category/cs/today). |

## Examples

Sample requests

```
GET /quota HTTP/1.1 
Content-Type: application/json
Common request parameters
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<amk_cluster_quota>20</amk_cluster_quota>
<ask_cluster_quota>3</ask_cluster_quota>
<cluster_nodepool_quota>10</cluster_nodepool_quota>
<cluster_quota>50</cluster_quota>
<node_quota>100</node_quota>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "amk_cluster_quota" : 20,
  "ask_cluster_quota" : 3,
  "cluster_nodepool_quota" : 10,
  "cluster_quota" : 50,
  "node_quota" : 100
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


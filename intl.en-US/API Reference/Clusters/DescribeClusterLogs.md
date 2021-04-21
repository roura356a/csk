# DescribeClusterLogs

Queries the logs of a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterLogs&type=ROA&version=2015-12-15)

## Request syntax

```
GET /clusters/ClusterId/logs HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the ACK cluster. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
[ {
  "ID" : Long,
  "cluster_id" : "String",
  "cluster_log" : "String",
  "log_level" : "String",
  "created" : "String",
  "updated" : "String"
} ]
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array| |The list of the returned logs. |
|ID|Long|590749245|The ID of the log. |
|cluster\_id|String|c23421cfa74454bc8b37163fd19af\*\*\*|The ID of the ACK cluster. |
|cluster\_log|String|start to update cluster status to CREATE\_COMPLETE|The content of the log. |
|log\_level|String|0004|The level of the log. |
|created|String|2020-09-11T10:11:51+08:00|The time when the log was generated. |
|updated|String|2020-09-11T10:11:51+08:00|The time when the log was updated. |

## Examples

Sample requests

```
GET /clusters/c106f377e16f34eb1808d6b9362c9****/logs HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<0>
    <ID>590749245</ID>
    <cluster_id>c23421cfa74454bc8b37163fd19af***</cluster_id>
    <cluster_log>start to update cluster status to CREATE_COMPLETE</cluster_log>
    <log_level>0004</log_level>
    <created>2020-09-11T10:11:51+08:00</created>
    <updated>2020-09-11T10:11:51+08:00</updated>
</0>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

[ {
  "ID" : 590749245,
  "cluster_id" : "c23421cfa74454bc8b37163fd19af***",
  "cluster_log" : "start to update cluster status to CREATE_COMPLETE",
  "log_level" : "0004",
  "created" : "2020-09-11T10:11:51+08:00",
  "updated" : "2020-09-11T10:11:51+08:00"
} ]
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


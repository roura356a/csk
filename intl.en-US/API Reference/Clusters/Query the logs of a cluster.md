# Query the logs of a cluster

You can call DescribeClusterLogs to query the logs of a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterLogs&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /clusters/{ClusterId}/logs http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array of loginfo| |The returned data. |
|ID|Long|"590749245"| |
|cluster\_id|String|"c23421cfa74454bc8b37163fd19af\*\*\*"| |
|cluster\_log|String|"start to update cluster status to CREATE\_COMPLETE"| |
|created|String|"2020-09-11T10:11:51+08:00"| |
|log\_level|String|"\\\\\\\\u0004"| |
|updated|String|"2020-09-11T10:11:51+08:00"| |

## Examples

Sample requests

```
GET /clusters/[ClusterId]/logs HTTP/1.1
Common request parameters
{
    "ClusterId":"c106f377e16f34eb1808d6b9362c9****"
}
```

Sample success responses

`XML` format

```
<PARAM_KEY>
      <cluster_id>c23421cfa74454bc8b37163fd19af***</cluster_id>
      <cluster_log>start to update cluster status to CREATE_COMPLETE</cluster_log>
      <created>2020-09-11T10:11:51+08:00</created>
      <log_level>\\u0004</log_level>
      <ID>590749245</ID>
      <updated>2020-09-11T10:11:51+08:00</updated>
</PARAM_KEY>
```

`JSON` format

```
{
    "PARAM_KEY": [
        {
            "cluster_id": "c23421cfa74454bc8b37163fd19af***",
            "cluster_log": "start to update cluster status to CREATE_COMPLETE",
            "created": "2020-09-11T10:11:51+08:00",
            "log_level": "\\\\u0004",
            "ID": "590749245",
            "updated": "2020-09-11T10:11:51+08:00"
        }
    ]
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


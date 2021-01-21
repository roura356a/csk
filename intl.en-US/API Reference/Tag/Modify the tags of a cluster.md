# Modify the tags of a cluster

You can call ModifyClusterTags to modify the tags of a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ModifyClusterTags&type=ROA&version=2015-12-15)

## Request headers

This operation uses the common request header only. For more information, see Common parameters.

## Request syntax

```
POST /clusters/[ClusterId]/tags HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the cluster. |
|key|String|Yes|type|The name of the tag to be modified. |
|value|String|Yes|web|The value of the tag to be modified. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|requestId|String|687C5BAA-D103-4993-884B-C35E4314A1E1|The ID of the request. |

## Examples

Sample requests

```
POST /clusters/[ClusterId]/tags HTTP/1.1
Common request header
[
  {
    "key": "xxxxxxxx",
    "value": "xxxxxxxx"
  }
]
```

Sample success responses

`XML` format

```
<cluster_id>cb95aa626a47740afbf6aa099b650****</cluster_id>
<request_id>687C5BAA-D103-4993-884B-C35E4314A1E1</request_id>
<task_id>T-5a54309c80282e39ea00002f</task_id>
```

`JSON` format

```
{
    "cluster_id": "cb95aa626a47740afbf6aa099b650****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


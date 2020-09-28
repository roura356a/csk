# Query details of cluster add-ons

You can call DescribeClusterAddonsVersion to query the details of all add-ons that are installed for a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterAddonsVersion&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses common request parameters only. For more information, see Common parameters.

## Request syntax

```
GET /clusters/[ClusterId]/components/version HTTPS|HTTP
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|can\_upgrade|Boolean|false|Indicates whether the add-on can be upgraded. |
|changed|String|""|Indicates whether the add-on is updated by the user. |
|component\_name|String|alicloud-disk-controller|The name of the add-on. |
|message|String|ok to upgrade|The additional message that indicates whether the add-on can be upgraded. |
|next\_version|String|""|The target version to which the add-on can be upgraded. |
|required|Boolean|true|Indicates whether the add-on is required. |
|template|String|\*\*\*\*|The latest template. |
|version|String|v1.14.8.37-bd3fd891-aliyun|The current version of the add-on. |

## Examples

Sample requests

```
GET /clusters/[ClusterId]/components/version HTTP/1.1
Common request parameters
{
"ClusterId": "c82e6987e2961451182edacd74faf****"
}
```

Sample success responses

`XML` format

```
<template>****</template>
<component_name>alicloud-disk-controller</component_name>
<can_upgrade>false</can_upgrade>
<message>ok to upgrade</message>
<version>v1.14.8.37-bd3fd891-aliyun</version>
<required>true</required>
<next_version>""</next_version>
<changed>""</changed>
```

`JSON` format

```
{"template":"****","component_name":"alicloud-disk-controller","can_upgrade":"false","message":"ok to upgrade","version":"v1.14.8.37-bd3fd891-aliyun","required":"true","next_version":"\"\"","changed":"\"\""}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


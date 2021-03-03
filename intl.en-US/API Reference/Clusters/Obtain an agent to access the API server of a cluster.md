# Obtain an agent to access the API server of a cluster

You can call DescribeExternalAgent to obtain an agent to access the API server of a cluster. You can deploy the agent to a cluster. This allows you to access the API server of the cluster.

For more information about cluster connection, see [Register an external Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Register an external Kubernetes cluster.md).

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeExternalAgent&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses only common request parameters. For more information, see Common parameters.

## Request syntax

```
get /k8s/{ClusterId}/external/agent/deployment http|https
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the cluster. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|config|String|"\*\*\*"|The information about the agent. |

## Examples

Sample requests

```
GET /k8s/[ClusterId]/external/agent/deployment HTTP/1.1
Common request parameters
{
    "ClusterId":"c106f377e16f34eb1808d6b9362c9****"
}
```

Sample success responses

`XML` format

```
<config>***</config>
```

`JSON` format

```
{
    "config": "***"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


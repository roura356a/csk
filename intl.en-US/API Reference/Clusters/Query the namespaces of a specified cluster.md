# Query the namespaces of a specified cluster

You can call the DescribeClusterNamespaces operation to query the namespaces of a specified cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterNamespaces&type=ROA&version=2015-12-15)

## Request syntax

```
GET /k8s/ClusterId/namespaces HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c68069cade53b4558924a924b415f\*\*\*\*|The ID of the cluster. |

## Response syntax

```
HTTP/1.1 200 OK
Content-Type:application/json

[ "String" ]
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
| |Array of String|kube-system|The names of the namespaces. |

## Examples

Sample requests

```
GET /k8s/c68069cade53b4558924a924b415f****/namespaces HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<0>kube-system</0>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

[ "kube-system" ]
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


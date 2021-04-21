# DescribeExternalAgent

Obtains the agent configurations of a specified Container Service for Kubernetes \(ACK\) cluster that is used to register external Kubernetes clusters. You can deploy the agent in an external Kubernetes cluster. This allows the external Kubernetes cluster to access the API server of this ACK cluster.

For more information, see [Register an external Kubernetes cluster](~~121053~~).

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeExternalAgent&type=ROA&version=2015-12-15)

## Request syntax

```
GET /k8s/ClusterId/external/agent/deployment?PrivateIpAddress=String HTTP/1.1 
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c106f377e16f34eb1808d6b9362c9\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|PrivateIpAddress|String|No|true|Specifies whether to obtain the credential that is used to access the internal network.

 -   `true`: obtains the credential that is used to access the internal network.
-   `false`: obtains the credential that is used to access the Internet.

 Default value: `false`. |

## Response syntax

```
HTTP/1.1 200
Content-Type:application/json
{
  "config" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|config|String|apiVersion: v1\*\*\*\*|The agent configurations in YAML format. |

## Examples

Sample requests

```
GET /k8s/c106f377e16f34eb1808d6b9362c9****/external/agent/deployment?PrivateIpAddress=true HTTP/1.1 
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<config>apiVersion: v1****</config>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "config" : "apiVersion: v1****"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


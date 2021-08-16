# Query the kubeconfig file of a cluster

You can call the DescribeClusterUserKubeconfig operation to query the kubeconfig file of a Container Service for Kubernetes \(ACK\) cluster. The kubeconfig file contains the details of the current user and is used to configure access to a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterUserKubeconfig&type=ROA&version=2015-12-15)

## Request syntax

```
GET /k8s/ClusterId/user_config?PrivateIpAddress=Boolean&TemporaryDurationMinutes=Long HTTP/1.1
Content-Type:application/json
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c5b5e80b0b64a4bf6939d2d8fbbc5\*\*\*\*|The ID of the ACK cluster that you want to query |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|PrivateIpAddress|Boolean|No|true|Specifies whether to obtain the credential that is used to connect to the cluster through the internal network. Valid values:

-   `true`: obtains the credential that is used to connect to the cluster over the internal network.
-   `false`: obtains the credential that is used to connect to the cluster over the Internet.

Default value: `false`. |
|TemporaryDurationMinutes|Long|No|15|The validity period of a temporary kubeconfig file. Unit: minutes. Valid values: 15 to 4320.

**Note:**

-   If you specify this parameter, the returned kubeconfig file is valid within the specified time period and will expire at the time specified by `expiration` in the response.
-   If you do not specify this parameter, the returned kubeconfig file is valid within a long period of time and will expire at the time specified by `expiration` in the response. |

## Response syntax

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "config" : "String",
  "expiration" : "String"
}
```

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|config|String|apiVersion: v1\*\*\*\*|The content of the kubeconfig file. For more information about how to check the content of the kubeconfig file of an ACK cluster, see [Configure cluster credentials](~~86494~~). |
|expiration|String|2024-03-10T09:56:17Z|The expiration time of the kubeconfig file. The value is in the RFC3339 format and is displayed in UTC. |

## Examples

Sample requests

```
GET /k8s/c5b5e80b0b64a4bf6939d2d8fbbc5****/user_config?PrivateIpAddress=true&TemporaryDurationMinutes=15 HTTP/1.1
Host:cs.aliyuncs.com
Content-Type:application/json
```

Sample success responses

`XML` format

```
HTTP/1.1 200 OK
Content-Type:application/xml

<config>
apiVersion: v1
clusters:
- cluster:
...
</config>
<expiration>2021-03-25T10:01:20Z</expiration>
```

`JSON` format

```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "config" : "\napiVersion: v1\nclusters:\n- cluster:\n...\n",
  "expiration" : "2021-03-25T10:01:20Z"
}
```

## Errors

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


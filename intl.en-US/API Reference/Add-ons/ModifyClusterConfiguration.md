# ModifyClusterConfiguration

Modifies a control plane component in a specified managed Kubernetes cluster by configuring ConfigMaps for the component. You can call this operation to manage a managed Kubernetes cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=ModifyClusterConfiguration&type=ROA&version=2015-12-15)

## Request syntax

```
PUT /clusters/ClusterId/configuration HTTP/1.1 
Content-Type:application/json
{
  "customize_config" : [ {
    "name" : "String",
    "configs" : [ {
      "key" : "String",
      "value" : "String"
    } ]
  } ]
}
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|cc0f87de0b8fb403f86e10e204f83\*\*\*\*|The ID of the Container Service for Kubernetes \(ACK\) cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|customize\_config|Array|No| |The details of the custom configurations. |
|name|String|No|kube-apiserver|The name of the component. |
|configs|Array|No| |The custom configurations. |
|key|String|No|MaxRequestsInflight|The name of the ConfigMap. |
|value|String|No|100|The value of the ConfigMap. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
PUT /clusters/cc0f87de0b8fb403f86e10e204f83****/configuration HTTP/1.1 
Content-Type:application/json
{
  "customize_config" : [ {
    "name" : "kube-apiserver",
    "configs" : [ {
      "key" : "MaxRequestsInflight",
      "value" : "100"
    } ]
  } ]
}
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


# InstallClusterAddons

Installs a component in a specified Container Service for Kubernetes \(ACK\) cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=InstallClusterAddons&type=ROA&version=2015-12-15)

## Request syntax

```
POST /clusters/ClusterId/components/install HTTP/1.1
Content-Type:application/json

[ {
  "name" : "String",
  "version" : "String",
  "config" : "String"
} ]
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c82e6987e2961451182edacd74faf\*\*\*\*|The ID of the ACK cluster. |

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
| |Array|No| |The request body parameters. |
|name|String|Yes|ags-metrics-collector|The name of the component that you want to install. |
|version|String|Yes|v1.0.0.2-cc3b2d6-aliyun|The version of the component.

 You can call DescribeClusterAddonsVersion to query the component versions. |
|config|String|No|\{\\"IngressDashboardEnabled\\":\\"true\\",\\"sls\_project\_name\\":\\"your\_sls\_project\_name\\"\}"\}|The component configurations. |

## Response syntax

```
HTTP/1.1 200 OK
```

## Response parameters

None

## Examples

Sample requests

```
POST /clusters/c82e6987e2961451182edacd74faf****/components/install HTTP/1.1 
Content-Type:application/json
[ {
  "name" : "ags-metrics-collector",
  "version" : "v1.0.0.2-cc3b2d6-aliyun",
  "config" : "{\\\"IngressDashboardEnabled\\\":\\\"true\\\",\\\"sls_project_name\\\":\\\"your_sls_project_name\\\"}\"}"
} ]
```

Sample success responses

`JSON` format

```
HTTP/1.1 200 OK
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


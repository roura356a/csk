# Obtain a kubeconfig file of a cluster

You can call DescribeClusterUserKubeconfig to obtain a kubeconfig file of a cluster. The kubeconfig file contains the details of the current user and is used to configure access to a cluster.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=CS&api=DescribeClusterUserKubeconfig&type=ROA&version=2015-12-15)

## Request parameter types

This operation uses common request parameters only. For more information, see Common parameters.

## Request syntax

```
 http://provision.cs.aliyun-inc.com http
```

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|ClusterId|String|Yes|c5b5e80b0b64a4bf6939d2d8fbbc5\*\*\*\*|The ID of the cluster. |
|PrivateIpAddress|Boolean|No|true|Specifies whether to return the kubeconfig file that is used to access the cluster from the internal network. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|config|String|"\*\*\*"|The content of the kubeconfig file. |

## Examples

Sample requests

```
GET /k8s/[ClusterId]/user_config HTTP/1.1
Common request parameters
{
    "ClusterId":"c5b5e80b0b64a4bf6939d2d8fbbc5****"
}
```

Sample success responses

`XML` format

```
<config>
apiVersion: v1
clusters:
- cluster:
    server: https://192.168.0.**:6443
    certificate-authority-data: ***
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: "kubernetes-admin"
  name: kubernetes-admin-c23421cfa74454bc8b37163fd19af****
current-context: kubernetes-admin-c23421cfa74454bc8b37163fd19af****
kind: Config
preferences: {}
users:
- name: "kubernetes-admin"
  user:
    client-certificate-data: ***
    client-key-data: ***
</config>
```

`JSON` format

```
{
	"config": "\napiVersion: v1\nclusters:\n- cluster:\n    server: https://192.168.0.**:6443\n    certificate-authority-data: ***\n  name: kubernetes\ncontexts:\n- context:\n    cluster: kubernetes\n    user: \"kubernetes-admin\"\n  name: kubernetes-admin-c23421cfa74454bc8b37163fd19af****\ncurrent-context: kubernetes-admin-c23421cfa74454bc8b37163fd19af****\nkind: Config\npreferences: {}\nusers:\n- name: \"kubernetes-admin\"\n  user:\n    client-certificate-data: ***\n    client-key-data: ***\n"
}
```

## Error codes

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/CS).


# 获取集群kubeconfig接口

调用DescribeClusterUserKubeconfig返回包含当前登录用户身份信息的Kubernetes集群访问kubeconfig。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=CS&api=DescribeClusterUserKubeconfig&type=ROA&version=2015-12-15)

## 请求头

该接口使用公共请求头，无特殊请求头。请参见公共请求参数文档。

## 请求语法

```
GET /k8s/{ClusterId}/user_config 
```

## 请求参数

|名称|类型|位置|是否必选|示例值|描述|
|--|--|--|----|---|--|
|ClusterId|String|Path|是|c5b5e80b0b64a4bf6939d2d8fbbc5\*\*\*\*|集群ID。 |
|PrivateIpAddress|Boolean|Query|否|true|是否获取内网连接配置。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|config|String|\*\*\*|集群访问配置。 |

## 示例

请求示例

```
GET /k8s/c5b5e80b0b64a4bf6939d2d8fbbc5****/user_config?PrivateIpAddress=true
公共请求头
```

正常返回示例

`XML` 格式

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

`JSON` 格式

```
{
	"config": "\napiVersion: v1\nclusters:\n- cluster:\n    server: https://192.168.0.**:6443\n    certificate-authority-data: ***\n  name: kubernetes\ncontexts:\n- context:\n    cluster: kubernetes\n    user: \"kubernetes-admin\"\n  name: kubernetes-admin-c23421cfa74454bc8b37163fd19af****\ncurrent-context: kubernetes-admin-c23421cfa74454bc8b37163fd19af****\nkind: Config\npreferences: {}\nusers:\n- name: \"kubernetes-admin\"\n  user:\n    client-certificate-data: ***\n    client-key-data: ***\n"
}
```

## 错误码

访问[错误中心](https://error-center.aliyun.com/status/product/CS)查看更多错误码。


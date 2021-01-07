# 创建Serverless集群

通过执行arc-cs cluster create-ask命令创建一个新的Serverless集群。

关于该命令的详细参数说明，请参见API文档[创建Serverless Kubernetes集群](/cn.zh-CN/API参考/集群/创建集群/创建Serverless Kubernetes集群.md)。

## CLI示例

请求示例：

```
arc-cs cluster create-ask \
--name test-cli2-ask \
--service-cidr 172.19.0.0/20 \
--vpcid vpc-bp1qjw8u2gk7jeipp**** \
--vswitch-ids '["vsw-bp1910rnb01vtrvfz****"]'
```

返回结果示例：

```
{
  "clusterId": "c16ebf3e95e75464eba259a57621c****",
  "requestId": "DB3F3DC5-6BF4-48B7-8664-28B361D5****",
  "taskId": "T-5ff3dfbbbb6b2d29ed00****"
}
```


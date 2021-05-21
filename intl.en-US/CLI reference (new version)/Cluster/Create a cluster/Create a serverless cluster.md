# Create a serverless cluster

You can run the arc-cs cluster create-ask command to create a serverless Kubernetes cluster.

For more information about this command, see [Create an ASK cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create an ASK cluster.md).

## Examples

Sample request:

```
arc-cs cluster create-ask \
--name test-cli2-ask \
--service-cidr 172.19.0.0/20 \
--vpcid vpc-bp1qjw8u2gk7jeipp**** \
--vswitch-ids '["vsw-bp1910rnb01vtrvfz****"]'
```

Sample response:

```
{
  "clusterId": "c16ebf3e95e75464eba259a57621c****",
  "requestId": "DB3F3DC5-6BF4-48B7-8664-28B361D5****",
  "taskId": "T-5ff3dfbbbb6b2d29ed00****"
}
```


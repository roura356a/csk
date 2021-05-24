# Create a managed cluster

You can run the arc-cs cluster create-managed command to create a managed Kubernetes cluster.

For more information about this command, see [CreateCluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a managed Kubernetes cluster.md).

## Examples

Sample request:

```
arc-cs cluster create-managed \
--login-password Hello1234 \
--worker-instance-types '["ecs.g6a.xlarge"]' \
--num-of-nodes 2 \ 
--worker-system-disk-category cloud_essd \
--worker-system-disk \
--size 120 \
--vpcid vpc-bp1qjw8u2gk7jeipp**** \
--name test-cli2 \
--region cn-hangzhou \
--container-cidr 172.22.0.0/16 \ 
--service-cidr 172.19.0.0/20 \
--vswitch-ids '["vsw-bp1910rnb01vtrvfz****"]'
```

Sample response:

```
{
  "clusterId": "c7abaabfee1584097b7d1ec114bb4****",
  "requestId": "0A428591-47BC-44E9-B1D8-7ABBED6D****",
  "taskId": "T-5ff3dce9bb6b2d29ed00****"
}
```


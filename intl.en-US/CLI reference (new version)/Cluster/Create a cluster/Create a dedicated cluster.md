# Create a dedicated cluster

You can run the arc-cs cluster create-kubernetes command to create a dedicated Kubernetes cluster.

For more information about this command, see [Create a dedicated Kubernetes cluster](/intl.en-US/API Reference/Clusters/Create a cluster/Create a dedicated Kubernetes cluster.md).

## Examples

Sample request:

```
arc-cs cluster create-kubernetes \
--login-password Hello1234 \
--snat-entry true \
--worker-system-disk-category cloud_ssd \
--worker-system-disk-size 200 \
--vpcid vpc-bp1df11js8nc8irlz**** \
--worker-instance-types '["ecs.hfc7.xlarge"]' \
--master-system-disk-category cloud_ssd \
--master-system-disk-size 200 \
--num-of-nodes 2 \
--master-instance-types '["ecs.hfc7.xlarge"]' \
--master-vswitch-ids '["vsw-bp1oq6ualk6fyz01g****","vsw-bp1oq6ualk6fyz01g****","vsw-bp1oq6ualk6fyz01g****"]' \
--worker-vswitch-ids '["vsw-bp1oq6ualk6fyz01g****"]' \
--region cn-hangzhou \
--name test-cli \
--service-cidr 172.19.0.0/20 \
--container-cidr 172.22.0.0/16
```

Sample response:

```
{
  "clusterId": "c644b367c5a834b18baf9c696bfb3****",
  "requestId": "5E6E63A5-68B8-433E-AA3E-6A4BAA5D****",
  "taskId": "T-5feae4e97720867d4d00****"
}
```


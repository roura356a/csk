# Add existing ECS instances to a cluster

You can run the following command to add existing Elastic Compute Service \(ECS\) instances to a Container Service for Kubernetes \(ACK\) cluster: `arc cs node attach <clusterid> --password <password> --instances '["<instanceid1>","<instanceid2>"]'`.

For more information about this command, see [Add existing ECS instances to a cluster](/intl.en-US/API Reference/Nodes/Add existing ECS instances to a cluster.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc cs node attach c8cfc598fc84a41d6af96965f34ed**** --password Hello**** --instances '["i-bp1bw9dno0jghymp****","i-bp1bw9dno0jghymp****"]'
```

Sample response:

```
{
    "list": [
        {
            "code": "200",
            "instanceId": "i-2zee3oiwcyoz7kwd****",
            "message": "successful"
        },
        {
            "code": "200",
            "instanceId": "i-2ze0lgm3y6iylcbt****",
            "message": "successful"
        }
    ],
    "task_id": "T-5a544aff80282e39ea000039"
}
```


# Add existing ECS instances

Adds existing ECS instances to a cluster.

For more information about the API operation, see [AttachInstances](/intl.en-US/API Reference/Node pools/AttachInstances.md).

## API request and response formats

Request format

```
aliyun cs  POST /clusters/<cluster_id>/attach --header "Content-Type=application/json" --body "$(cat attach.json)"
```

Request parameters:

-   `--header`: You must set Content-Type to application/json.
-   `--body`: The content that is sent to the server. The content can be saved in a local file and must be in valid JSON format. The `create.json` file contains the following content:

```
{ "password": "root password of the ECS instance", "instances": "instances that you want to add to the cluster", "format_disk": "whether to format data disks", "key_pair": "key pair" "tags": "node tags in an array" }
```

Sample responses

```
{
    "list": [
{            "code": "200",
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


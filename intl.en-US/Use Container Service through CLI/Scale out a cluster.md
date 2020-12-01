# Scale out a cluster

This topic describes how to increase the number of nodes in a Container Service for Kubernetes \(ACK\) cluster.

For more information about the API operation, see [Add worker nodes to a cluster](/intl.en-US/API Reference/Clusters/Expand a cluster/Add worker nodes to a cluster.md).

## API request and response formats

Request format

```
aliyun cs  PUT /clusters/<cluster_id> --header "Content-Type=application/json" --body "$(cat scale.json)"
```

The following parameters are included:

-   `--header`: Set Content-Type to application/json.
-   `--body`: the body of content that you want to send to the server. The content must be in a valid JSON format and can be read from a local file. The following example shows the content of the `create.json` file:

Kubernetes cluster

```
{
    "disable_rollback": "whether or not to roll back if the cluster fails to be scaled out or in",
    "timeout_mins": "timeout for creating the cluster",
    "worker_instance_type": "instance type of worker nodes",
    "login_password": "password used to log on to the node by using SSH",    
    "num_of_nodes": "number of worker nodes"    
    "tags": "the tag to be added to the node that you add to the cluster. It is an object in the array format."
}
```

Sample responses

```
HTTP/1.1 202 Accepted
<Public response header>
{
    "cluster_id": "Cccfd68c474454665ace07efce924****",
    "request_id": "687C5BAA-D103-4993-884B-C35E4314A1E1",
    "task_id": "T-5a54309c80282e39ea00002f"
}
```


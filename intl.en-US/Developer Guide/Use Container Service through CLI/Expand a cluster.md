# Expand a cluster {#reference_fvy_rxb_wdb .reference}

This topic describes how to increase the number of nodes in the cluster. For API descriptions, see [Scale out or in a cluster](reseller.en-US/Developer Guide/Cluster API list/Scale out or in a cluster.md#).

## API request and response {#section_tm4_vxb_wdb .section}

**Request format**

```
aliyun cs  PUT /clusters/<cluster_id> --header "Content-Type=application/json" --body "$(cat scale.json)"
```

Parameter descriptions:

-   `--header`: Specify Content-Type as application/json.
-   `--body`: This is the body content to be sent to the server. The content can be read from a local file and must be in JSON format. The content of `scale.json` is as follows:

**Kubernetes cluster**

```
{ "disable_rollback": "indicates whether to roll back the cluster if cluster expansion fails",
"timeout_mins": "timeout period for creating a cluster",

"worker_instance_type": "Worker instance type",
"worker_system_disk_category": "system disk type of Worker nodes",
"worker_system_disk_size": "system disk size of Worker nodes",
"login_password": "password used to log on to the node by using SSH", 
"image_id": "image ID",
"num_of_nodes": "number of Worker nodes"
}
```

**Response result**

```
{
    "cluster_id": "c61cf530524474386a7ab5a1c192a0d57",
    "request_id": "348D4C9C-9105-4A1B-A86E-B58F0F875575",
    "task_id": "T-5ad724ab94a2b109e8000004"
}
```


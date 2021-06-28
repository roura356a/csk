# View all clusters

Queries all clusters that you have created.

## API request and response formats

Request format

```
aliyun cs GET /clusters
```

Sample responses

```
[
    {
        "agent_version": "string",
        "cluster_id": "string",
        "created": "datetime",
        "external_loadbalancer_id": "string",
        "master_url": "string",
        "name": "string",
        "network_mode": "string",
        "region_id": "string",
        "security_group_id": "string",
        "size": "numbers",
        "state": "string",
        "updated": "datetime",
        "vpc_id": "string",
        "vswitch_id": "string"
    }
]
```


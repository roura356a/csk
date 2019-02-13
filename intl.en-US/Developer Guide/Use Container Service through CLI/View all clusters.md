# View all clusters {#reference_mqb_1vb_wdb .reference}

This topic describes how to views all the clusters that you have created through Alibaba Cloud Container Service for Kubernetes.

## API request and response {#section_my3_gvb_wdb .section}

**Request format**

```
aliyun cs GET /clusters
```

**Response result**

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


# View cluster information {#reference_a2f_pvb_wdb .reference}

This topic describes how to view the cluster details according to the cluster ID. For API descriptions, see [View a cluster](reseller.en-US/developer guide/Cluster API list/View a cluster.md#).

## API request and response {#section_yhs_vvb_wdb .section}

**Request format**

```
aliyun cs GET /clusters/<cluster_id>
```

**Response result**

```
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
```


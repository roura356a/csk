# Create a custom node pool

You can run the following command to create a custom node pool: `arc-cs nodepools create <cluster_id> --count <instance_number> --nodepool-info '{xxx}' --scaling-group '{xxx}' --kubernetes-config '{xxx}'`.

For more information about this command, see [Create a node pool](/intl.en-US/API Reference/Node pools/Create a node pool.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs nodepools create caa73d115830746e0942c9554ecae**** \
--count 1 \
--nodepool-info '{
    "name":"custom-nodepool",
    "resource-group-id":"rg-acfmyvw3wjm****"
  }' \
--scaling-group '{
    "vswitch-ids":["vsw-2ze0ua3ggkgwmfcd0****"],
    "instance-charge-type":"PostPaid",
    "instance-types":["ecs.n1.medium"],
    "system-disk-category":"cloud_efficiency",
    "system-disk-size":120,
    "data-disks":[{"category":"cloud_efficiency","size":120,"encrypted":"false"}],
    "key-pair":"sian-sshkey",
    "platform":"CentOS",
    "security-group-id":"sg-2ze1ld632d8265bh****",
    "tags":[{"key":"ecs_tags_key","value":"ecs_tags_value"}]
  }' \
--kubernetes-config '{
    "cms-enabled":true,
    "cpu-policy":"none",
    "labels":[{"key":"node_tags_key","value":"node_tags_value"}],
    "runtime":"docker",
    "runtime-version":"19.03.5"
  }'
```

Sample response:

```
{
  "nodepoolId": "npaf26367f491a4d3a8e5d9623ce43****"
}
```


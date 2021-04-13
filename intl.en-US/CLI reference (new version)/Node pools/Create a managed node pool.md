# Create a managed node pool

You can run the following command to create a managed node pool: `arc-cs nodepools create <cluster_id> --count <instance_number> --nodepool-info '{xxx}' --scaling-group '{xxx}' --kubernetes-config '{xxx}' --management '{xxx}'`.

For more information about this command, see [Create a node pool](/intl.en-US/API Reference/Node pools/Create a node pool.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs nodepools create caa73d115830746e0942c9554ecae**** \
--count 1 \
--nodepool-info '{
    "name":"management-nodepool",
    "resource-group-id":"rg-acfmyvw3wjm****"
    }' \
--scaling-group '{
    "vswitch-ids":["vsw-2ze0ua3ggkgwmfcd0****"],
    "instance-types":["ecs.n4.large"],
    "instance-charge-type":"PostPaid",
    "system-disk-category":"cloud_efficiency",
    "system-disk-size":120,
    "data-disks":[{"category":"cloud_ssd","size":40,"encrypted":"true"}],
    "key-pair":"sian-sshkey",
    "security-group-id":"sg-2ze1ld632d8265bh****",
    "platform":"CentOS"
    }' \
--kubernetes-config '{
    "cpu-policy":"none",
    "cms-enabled":true,
    "runtime":"docker",
    "runtime-version":"19.03.5"
    }' \
--management '{
  "enable":true,
  "auto-repair":true,
  "upgrade-config":{"auto-upgrade":true,"maxUnavailable":1}
}'
```

Sample response:

```
{
  "nodepoolId": "npc8cd27b44eb5461a9c1eda9d3162****"
}
```


# Create a managed node pool that supports auto scaling

You can run the following command to create a managed node pool that supports auto scaling: `arc-cs nodepools create <cluster_id> --nodepool-info '{xxx}' --scaling-group '{xxx}' --kubernetes-config '{xxx}' --auto-scaling '{xxx}' --management '{xxx}'`.

For more information about this command, see [Create a node pool](/intl.en-US/API Reference/Node pools/Create a node pool.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs nodepools create caa73d115830746e0942c9554ecae**** \
--nodepool-info '{
    "name":"management-nodepool",
    "resource-group-id":"rg-acfmyvw3wjm****"
  }' \
--scaling-group '{
    "vswitch-ids":["vsw-2ze0ua3ggkgwmfcd0****"],
    "instance-types":["ecs.n2.medium"],
    "instance-charge-type":"PostPaid",
    "system-disk-category":"cloud_efficiency",
    "system-disk-size":120,
    "data-disks":[{"category":"cloud_ssd","size":40,"encrypted":"true"}],
    "key-pair":"sian-sshkey",
    "security-group-id":"sg-2ze1ld632d8265bh****",
    "platform":"CentOS"
  }' \
--kubernetes-config '{
    "cpu_policy":"none",
    "cms_enabled":true,
    "labels":[{"key":"workload_type","value":"cpu"}],
    "runtime":"docker",
    "runtime-version":"19.03.5"
    }' \
--auto-scaling '{
    "enable":true,
    "max_instances":10,
    "min_instances":1,
    "type":"cpu"
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
  "nodepoolId": "npe95cfce093df4caf80ce5a187664****"
}
```


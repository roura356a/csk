# Create a node pool that supports auto scaling

You can run the following command to create a node pool that supports auto scaling: `arc-cs nodepools create <cluster_id> --nodepool-info '{xxx}' --scaling-group '{xxx}' --kubernetes-config '{xxx}' --auto-scaling '{xxx}'`.

For more information about this command, see [Create a node pool](/intl.en-US/API Reference/Node pools/Create a node pool.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-ack nodepools create caa73d115830746e0942c9554ecae**** \
--nodepool-info '{
    "name":"auto-scaling-nodepool",
    "resource-group-id":"rg-acfmyvw3wjm****"
    }' \
--scaling-group '{
    "vswitch-ids":["vsw-2ze0ua3ggkgwmfcd0****"],
    "system-disk-category":"cloud_efficiency",
    "system-disk-size":120,
    "instance-types":["ecs.n1.medium"],
    "instance-charge-type":"PostPaid",
    "key-pair":"sian-sshkey",
    "platform":"CentOS",
    "image-id":"centos_7_8_x64_20G_alibase_20200914.vhd",
    "scaling-policy":"release",
    "security-group-id":"sg-2ze1ld632d8265bh****"
    }' \
--kubernetes-config '{
    "cpu-policy":"none",
    "cms-enabled":true,
    "labels":[{"key":"workload_type","value":"cpu"}],
    "user-data":"IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvLCBBQ0sh****",
    "runtime":"docker",
    "runtime-version":"19.03.5"
    }' \
--auto-scaling '{
    "enable":true,
    "max-instances":10,
    "min-instances":1,
    "type":"cpu"
    }' 
```

Sample response:

```
{
  "nodepoolId": "np71386d93a7f443cb9de3fa26d96e****"
}
```


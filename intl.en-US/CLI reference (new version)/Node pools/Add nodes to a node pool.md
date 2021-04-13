# Add nodes to a node pool

You can run the following command to add nodes to a node pool: `arc-cs nodepools scaleout <cluster_id> <nodepool_id> --count <instance_scaleout_number>`.

**Note:** This command is applicable to only custom node pools.

For more information about this command, see [ScaleClusterNodePool](/intl.en-US/API Reference/Node pools/ScaleClusterNodePool.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs nodepools scaleout caa73d115830746e0942c9554ecae**** npd4cc395c77ee467b9c68db3cabed**** \
--count 1
```

Sample response:

```
{
  "taskId": "T-60190e374d345a4821000052"
}
```


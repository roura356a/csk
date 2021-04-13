# Modify the configurations of a node pool

You can run the following command to modify the configurations of a node pool: `arc-cs nodepools update <cluster_id> <nodepool_id> --auto-scaling '{xxx}'`.

For more information about this command, see [Update node pool information](/intl.en-US/API Reference/Node pools/Update node pool information.md).

## Example by using Alibaba Cloud CLI

Sample request:

-   Example 1: Modify the minimum number of nodes of a node pool that supports auto scaling

    ```
    arc-cs nodepools update caa73d115830746e0942c9554ecae**** npea4e5931b10d4474adb39894b810**** \   
    --auto-scaling '{
        "enable":true,
        "max-instances":10,
        "min-instances":2,
        "type":"cpu",
        "is-bond-eip":true,
        "eip-internet-charge-type":"PayByBandwidth",
        "eip-bandwidth":5
        }'
    ```

-   Example 2: Modify the name of a managed node pool

    ```
    arc-cs nodepools update caa73d115830746e0942c9554ecae**** npc8cd27b44eb5461a9c1eda9d3162**** \   
    --nodepool-info '{
        "name":"management-nodepool-modification",
        "resource-group-id":"rg-acfmyvw3wjm****"
      }'
    ```


Sample response:

```
{
  "taskId": "T-6019166e80282e4b8000010d"
}
```


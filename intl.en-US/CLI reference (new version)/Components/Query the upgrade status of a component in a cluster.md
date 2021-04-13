# Query the upgrade status of a component in a cluster

You can run the following command to query the upgrade status of a component in a cluster: `arc-cs addon get-upgrade-status <cluster_id> --component-ids '["component_name"]'`.

For more information about this command, see [Query the upgrade statuses of cluster add-ons](/intl.en-US/API Reference/Add-ons/Query the upgrade statuses of cluster add-ons.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs addon get-upgrade-status caa73d115830746e0942c9554ecae**** \ 
--component-ids '["metrics-server"]'
```

Sample response:

```
{
    "metric-server":{
        "tasks":{
            "created_at":"2020-12-28T17:00:32Z",
            "finished_at":"2020-12-28T17:05:54Z",
            "is_canceled":false,
            "status":"Success"
        },
        "addon_info":{
            "component_name":"metric-server",
            "version":"",
            "ready_to_upgrade":"true"
        }
    }
}
```


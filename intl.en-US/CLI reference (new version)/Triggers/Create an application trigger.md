# Create an application trigger

You can run the `arc-cs trigger create` command to create an application trigger.

For more information about this command, see [Create an application trigger](/intl.en-US/API Reference/Applications/Create an application trigger.md).

## CLI example

Sample request:

```
arc-cs trigger create  \
--cluster-id c29d47910794948e2a42925693170**** \
--project-id default/nginx \
--action redeploy \
--type deployment
```

Sample output:

```
{
  "id": "146905",
  "clusterId": "c29d47910794948e2a42925693170****",
  "projectId": "default/nginx",
  "type": "",
  "action": "redeploy"
}
```


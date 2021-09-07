# Query application triggers

You can run the `arc-cs trigger get <clusterId>` command to query application triggers in a specified cluster.

For more information about this command, see [Query an application trigger](/intl.en-US/API Reference/Applications/Query an application trigger.md).

## CLI example

Sample request:

```
arc-cs trigger get c29d47910794948e2a42925693170**** \
--namespace default \
--type deployment \
--name ng \
--action redeploy
```

Sample output:

```
[
  {
    "id": "146905",
    "name": "",
    "clusterId": "c29d47910794948e2a42925693170****",
    "projectId": "default/ng",
    "type": "deployment",
    "action": "redeploy",
    "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpX****.eyJjbHVzdGVySWQiOiJjMjlkNDc5MTA3OTQ5NDhlMmE0MjkyNTY5MzE3MDkzZmEiLCJpZCI6IjE0Njkw****.JdHATgluwwZdX6MYZUuPfgXbr5QmlsqVLCZGFZiGP0KkCbfYRIVZVSOfU6fuojy4GLrVvagFS2opfY66vkPEoQH3FMTsx0634mu6EKiZuUvFaPOgJiPOIxUSleomu8V3NVY0LwhQMOkcO****-YqQ****-1-Hafz0HqM2cD****"
  }
]
```


# Query the permissions of a RAM user

You can run the `arc-cs permission get <uid>` command to query the permissions that are granted to a specified Resource Access Management \(RAM\) user to manage a Container Service for Kubernetes \(ACK\) cluster. uid specifies the ID of a RAM user or RAM role.

For more information about this command, see [Query the permissions of a RAM user](/intl.en-US/API Reference/Permissions/Query the permissions of a RAM user.md).

## CLI example

Sample request:

```
arc-cs permission get 24034582727787****
```

Sample output:

```
[
  {
    "resourceId": "c09665845208b4fc2992b335be08b****",
    "resourceType": "cluster",
    "roleName": "",
    "roleType": "admin"
  },
  {
    "resourceId": "c3a29be5cd0ec4b0390d72d9d3be6****",
    "resourceType": "cluster",
    "roleName": "",
    "roleType": "admin"
  },
  {
    "resourceId": "c77cef6412922423a9d17a4452b17****",
    "resourceType": "cluster",
    "roleName": "",
    "roleType": "admin"
  },
  {
    "resourceId": "cca4bbb9654954e84a3182d89c579****",
    "resourceType": "cluster",
    "roleName": "",
    "roleType": "admin"
  }
]
```


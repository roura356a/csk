# Update authorization information of a RAM user

You can run the `arc-cs permission grant <uid>` command to update authorization information of a specified Resource Access Management \(RAM\) user to manage a Container Service for Kubernetes \(ACK\) cluster. uid specifies the ID of a RAM user or RAM role.

For more information about this command, see [GrantPermissions](/intl.en-US/API Reference/Permissions/GrantPermissions.md).

## CLI example

Sample request:

```
arc-cs permission grant 24034582727787**** \
--body '[{"cluster":"cca4bbb9654954e84a3182d89c57****","is-custom":false,"role-name":"dev","role-type":"cluster","is-ram-role":false}]'
```

No result is returned.


# Query all of the orchestration templates

You can run the `arc-cs template list` command to query all of the orchestration templates.

For more information about this command, see [DescribeTemplates](/intl.en-US/API Reference/Applications/DescribeTemplates.md).

## CLI example

Sample request:

```
arc-cs template list  \
--template-type kubernetes \
--page-num 1 \
--page-size 1
```

Sample output:

```
{
  "templates": [
    {
      "acl": "private",
      "id": "1abb8134-c07d-4c44-9a2a-e75e7629****",
      "name": "sss",
      "description": "test",
      "tags": "this is a test template",
      "template": "apiVersion: v1",
      "templateType": "kubernetes",
      "created": "2021-08-19T14:33:47+08:00",
      "updated": "2021-08-19T14:33:47+08:00",
      "templateWithHistId": "f927063e-af60-4942-9470-0e142310****"
    }
  ],
  "pageInfo": {
    "pageNumber": 1,
    "pageSize": 1,
    "totalCount": 12
  }
}
```


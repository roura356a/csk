# Update an orchestration template

You can run the `arc-cs template update <template_id>` command to update a specified orchestration template.

For more information about this command, see [UpdateTemplate](/intl.en-US/API Reference/Applications/UpdateTemplate.md).

## CLI example

Sample request:

```
arc-cs template update f927063e-af60-4942-9470-0e142310**** \
--name Test \
--tags test \
--description this is a test template \
--template-type kubernetes \
--template apiVersion: v1
```

No result is returned.


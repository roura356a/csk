# Create an orchestration template

You can run the `arc-cs template create` command to create an orchestration template.

For more information about this command, see [CreateTemplate](/intl.en-US/API Reference/Applications/CreateTemplate.md).

## CLI example

Sample request:

```
arc-cs template create \
--name test-template \
--tags test \
--description test \
--template-type kubernetes \
--template '
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
'
```

Sample output:

```
{
  "templateId": "f927063e-af60-4942-9470-0e142310****"
}
```


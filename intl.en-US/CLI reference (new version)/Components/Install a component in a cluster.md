# Install a component in a cluster

You can run the following command to install a component in a cluster: `arc-cs addon install <cluster_id> --body '[{"name":"<component_name>","version":"<component_version>"}]'`.

For more information about this command, see [Install add-ons for a cluster](/intl.en-US/API Reference/Add-ons/Install add-ons for a cluster.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs addon install caa73d115830746e0942c9554ecae**** \
--body '[{"name":"kritis-validation-hook","version":"v0.2.7.2-g5fa671a-aliyun"}]'
```

No result is returned.


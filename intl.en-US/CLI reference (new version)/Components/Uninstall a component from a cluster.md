# Uninstall a component from a cluster

You can run the following command to uninstall a component from a cluster: `arc-cs addon uninstall <cluster_id>`.

For more information about this command, see [UnInstallClusterAddons](/intl.en-US/API Reference/Add-ons/UnInstallClusterAddons.md).

## Example by using Alibaba Cloud CLI

Sample request:

```
arc-cs addon uninstall caa73d115830746e0942c9554ecae**** \
--addons '[{"name":"kritis-validation-hook"}]'
```

No result is returned.


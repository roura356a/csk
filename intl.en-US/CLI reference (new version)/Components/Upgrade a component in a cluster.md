# Upgrade a component in a cluster

You can run the `arc-cs addon upgrade <cluster_id> --body '[{"name":"<component_name>","version":"<component_version>"}]'` command to upgrade a specified component in a cluster.

For more information about this command, see [UpgradeClusterAddons](/intl.en-US/API Reference/Add-ons/UpgradeClusterAddons.md).

## CLI example

Sample request:

```
arc-cs addon upgrade c61f5440543bc40cd8dcf0ea1777e**** \
--body '[{"component-name":"kube-flannel-ds","next-version":"v0.13.0.2-466064b-aliyun","version":"v0.13.0.1-466064b-aliyun"}]'
```

No result is returned.


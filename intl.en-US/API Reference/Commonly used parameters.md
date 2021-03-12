Commonly used parameters {#doc-api-commonStruct}
================================================

addon {#addon}
--------------

Configurations of cluster components.

| Parameter |  Type   |                   Example                    |                                                                                                                                                                                                                                                                                Description                                                                                                                                                                                                                                                                                |
|-----------|---------|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | String  | nginx-ingress-controller                     | The name of a component.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| config    | String  | {\\"IngressSlbNetworkType\\":\\"internet\\"} | The configurations of a component.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| disabled  | Boolean | false                                        | Specifies whether a component is automatically installed in a newly created cluster. In addition to the required components, some optional components, such as Log Service components, are also installed when you create a cluster. You can set this parameter to disable the automatic installation of an optional component. You can install the component in the console or by calling the API after the cluster is created. Valid values: * `true`: disables the automatic installation of a component. * `false`: allows the automatic installation of a component. |

data_disk {#data-disk}
----------------------

Configurations of node data disks.

|        Parameter        |  Type  |           Example           |                                                                                                          Description                                                                                                           |
|-------------------------|--------|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| category                | String | cloud_ssd                   | The type of data disk. Valid values: * `cloud`: basic disk. * `cloud_efficiency`: ultra disk. * `cloud_ssd`: SSD. * `cloud_essd`: enhanced SSD. Default value: `cloud_efficiency`.                                             |
| size                    | Long   | 40                          | The size of a data disk. The size is measured in GiB. Valid values: 40 to 32768. Default value: `120`.                                                                                                                         |
| encrypted               | String | true                        | Specifies whether to encrypt a data disk. Valid values: * `true`: encrypts a data disk. * `false`: does not encrypt a data disk. Default value: `false`.                                                                       |
| auto_snapshot_policy_id | String | sp-2zej1nogjvovnz4z\*\*\*\* | The ID of an automatic snapshot policy. Automatic backup is performed for the cloud disk based on the specified automatic snapshot policy. By default, this parameter is empty. This indicates that automatic backup disabled. |

maintenance_window {#maintenance-window}
----------------------------------------

Configurations of the cluster maintenance window.

|    Parameter     |  Type   |     Example     |                                                                                               Description                                                                                               |
|------------------|---------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| enable           | Boolean | false           | Specifies whether to enable the cluster maintenance window. Valid values: * `true`: enables the cluster maintenance window. * `false`: disables the cluster maintenance window. Default value: `false`. |
| maintenance_time | String  | 03:00:00Z       | The start time of the cluster maintenance window. The value must be in the standard Golang time format. For example, 15:04:05Z.                                                                         |
| duration         | String  | 3h              | The duration of the cluster maintenance window. The duration ranges from 1 hour to 24 hours. Unit: h. Default value: 3h.                                                                                |
| weekly_period    | String  | Monday,Thursday | The day of the week when maintenance is performed. Separate multiple days with commas (,). Valid values: Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, and Sunday. Default value: `Thursday`. |

runtime {#runtime}
------------------

Configurations of container engines.

| Parameter |  Type  | Example |                                                                                                                                             Description                                                                                                                                              |
|-----------|--------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name      | String | docker  | The name of a container runtime. The following types of runtimes are supported by Container Service for Kubernetes (ACK). * `Sandboxed-Container.runv`: runs sandboxed containers, which provide improved isolation for application containers. * `docker`. * `containerd`. Default value: `docker`. |
| version   | String | 19.03.5 | The version of a container runtime. By default, the latest version is used. For more information about the changes to Sandboxed-Container, see [Release notes for Sandboxed-Container](~~160312~~).                                                                                                  |

tag {#tag}
----------

Configurations of labels.

| Parameter |  Type  | Example |       Description       |
|-----------|--------|---------|-------------------------|
| key       | String | env     | The `key` of a label.   |
| value     | String | prod    | The `value` of a label. |

taint {#taint}
--------------

Configurations of node taints.

| Parameter |  Type  |  Example   |                                                                                                                                                                                                                                                                                                      Description                                                                                                                                                                                                                                                                                                       |
|-----------|--------|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| key       | String | key        | The `key` of a taint.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| value     | String | value      | The `value` of a taint.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| effect    | String | NoSchedule | The scheduling policy. Valid values: *  `NoSchedule`: Pods that do not tolerate this taint are not scheduled to nodes with this taint. This policy affects only the scheduling process and takes effect for only pods to be scheduled. Scheduled pods are not affected. *  `NoExecute`: Pods that do not tolerate this taint are evicted after this taint is added to the node. *  `PreferNoSchedule`: a preferable policy on pods. Scheduled pods are not affected. If this taint is added to a node, the system tries to not schedule pods that do not tolerate this taint to the node. Default value: `NoSchedule`. |


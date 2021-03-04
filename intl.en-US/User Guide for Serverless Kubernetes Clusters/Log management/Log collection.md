# Log collection

## Enable ECI logging

ECI supports the log collection service. When you create an ECI through OpenAPI Explorer, set SlsEnable to true, and then enable ECI log collection. By default, the standard output and error logs of your ECI containers are collected to the Logstore of Log Service under your account. You do not need to manually configure any settings. The results are as follows:

**Default project******

Projects starting with eci-log-default-project- are default projects created by the system \(a default project is created for each region\).

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6449505061/p110734.png)

**Logstore**********

The default Logstore starts with eci-log-default-log-store-. It stores standard output and error logs of ECIs and can meet the needs of most scenarios.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6449505061/p110739.png)

**Machine group******

The default machine group starts with eci-log-default-machine-group. If you enable the log collection service, ECIs are added to the default machine group. You can view the ECIs added to the machine group by checking the status of the machine group.

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/6449505061/p110741.png)

**Configuration******

The default Logtail configuration starts with eci-log-default-config. By default, the Logtail configuration is generated in simple mode. To configure advanced settings, log on to the console and customize settings.

**Results******

![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/5106284161/p245415.png)

The preceding configurations are all default configurations and can meet the needs of most scenarios.

## Customize settings

Although the ECI service predefines basic settings, you may still need to customize some settings. By default, all ECIs are added to the default machine group of the default project. All logs are collected to the default Logstore. You may want to customize settings when you need to collect ECI logs to a custom Logstore for other projects, and add ECIs to different machine groups for different applications and services. In this case, you can customize the settings using the following two methods.

**Customize the settings in the Log Service console \(API\)******

You can log on to the Log Service console to create projects, logstores, and machine groups. Create a custom configuration for the Logstore and apply it to a selected machine group. Logs data is then collected to the new Logstore. Different configurations cannot be applied to the same log file. When you use the new Logstore, delete the default Logstore and configuration. Otherwise, log collection fails.

If the configuration in the Log Service console is too complicated to you, you can create and configure it using the ECI service.

**Customize settings by using the ECI service******

The ECI service can generate all default settings for you and allows you to customize the settings. For example, you can customize the project name, Logstore name, machine group name, and log collection directory. You can use environment variables of ******the first container** in ECI to pass parameters in the following format:

**Project name******

Optional. By default, the system automatically creates an ECI project for you. If the project name is not specified, then the default name is used. If a specific name is used, the project with the specified name is created. If the project is already created, logStore and config are created and added to this project.

```
-name: aliyun_logs_project
-value: {project name}
```

**Project name constraint******

-   The name can only contain lowercase letters, digits, and hyphens \(-\).
-   The name must start and end with a lowercase letter or digit.
-   The name must be 3 to 63 characters in length.

    **Note:** Invalid names are ignored and the default name will be used.


**Logstore******

Optional. A default Logstore is automatically created to store the standard output logs of the ECI containers. If this parameter is set, the ECI service will not generate a default value. Logs are collected to the specified Logstore. The ECI service creates or modifies configurations and applies them to the corresponding machine group.

**Note:** If you have not set a volume log directory \(see the following section\), the custom log directory must be a subdirectory of var/log/eci /.

```
-name: aliyun_log_logstore_{Logstore name} or aliyun_logs_{Logstore name}
-value: {Logging path}
```

**Logstore name constraint******

-   The name can contain lowercase letters, digits, hyphens \(-\), and underscores \(\_\).
-   The name must start and end with a lowercase letter or digit.
-   The name must be 3 to 63 characters in length.

**Note:** Invalid names are ignored and the default name will be used.

Set the number of shards in the Logstore****

For more information about shards, see [Shard](/intl.en-US/Product Introduction/Basic concepts/Shards.md).

Parameter settings

```
-name: aliyun_logs_{Logstore name}_shard
-value: {shard value}
```

Default value: 2. Valid values: 1 to 10.

Set the log retention period for the Logstore****.

Parameter settings

```
-name: aliyun_logs_{Logstore name}_ttl
-value: {ttl value}
```

Default value: 90. Valid values: 1 to 3650.

**\* Machine group name******

Optional. By default, ECIs are added to the default machine group created by the system. One region corresponds to one ECI. You can also choose to add ECIs to other machine groups that have already been created or to be created. Machine groups are helpful if you want to configure different log collection settings for applications and services deployed on different ECIs. The collection format is set as follows:

```
-name: aliyun_logs_machinegroup
-value: {Machine group name}
```

**Machine group name constraint******

-   The machine group name can contain only letters, digits, hyphens \(-\), and underscores \(\_\).
-   The name must start and end with a lowercase letter or digit.
-   The name must be 3 to 63 characters in length.

**Note:** The system will ignore any invalid names and use the default name instead.

## Collect user volume logs

By default, the system configures standard output and error logs for users, and collects and saves data into the default Logstore. In addition to basic logs, the system also collects user volume logs. User volume log collection is relatively flexible, and the corresponding collection directory needs to be set by users.

The standard output of ECI containers is logged in directory var/log/eci/\*/\*.log. By default, ECI automatically configures the directory for users. In most cases, a user does not need to change this directory.

The standard log collection directory of an ECI volume is a subdirectory under the directory where the volume is mounted, depending on your settings.

For example, EmptyDirVolume is mounted to the /pod/data/directory of the container. The log file of the volume can be a file in any subdirectory under /pod/data/. In this way, you can adjust the mounting directory and define a directory that best suits your needs.

**Create EmptyDirVolume**

```
'Volume.1.Name': 'default-volume',
'Volume.1.Type': 'EmptyDirVolume',
```

**Mount the volume to the container directory******

```
'Container.1.VolumeMount.1.Name': 'default-volume',
'Container.1.VolumeMount.1.MountPath': '/pod/data/',
'Container.1.VolumeMount.1.ReadOnly': False,
```

**Configure a Logstore******

'aliyun\_log\_logstore\_Store' is the directory used to store standard output of an ECI container, and 'aliyun\_log\_logstore\_Store2' is a directory used to store volume logs. It can match any file under the /pod/data/ directory.

```
'Container.1.EnvironmentVar.1.Key': 'aliyun_log_logstore_Store',
'Container.1.EnvironmentVar.1.Value': '/var/log/eci/*/*.log',
#'Container.1.EnvironmentVar.1.Key': 'aliyun_log_logstore_Store',
#'Container.1.EnvironmentVar.1.Value': 'stdout',
'Container.1.EnvironmentVar.2.Key': 'aliyun_log_logstore_Store2',
'Container.1.EnvironmentVar.2.Value': '/pod/data/*/*. *',
```

**Note:** When you set environment variables, note that stdout is equivalent to /var/log/eci/\*/\*.log.

**Effect**

As shown in the following figure, create a file under the mount directory of the volume, and then enter content.![](../images/p84508.png)

Open the corresponding Logstore. The content has been automatically collected by the Logstore.![](../images/p84509.png)

**Note:**

-   The log project must be in the same region as the ECI. Otherwise, the default ECI project is used.
-   If you do not define a Logstore by using ECI environment variables, the system configures the ECI standard output and error log collection settings for you. If you defined your own Logstore by using ECI environment variables, the system will not configure the ECI standard output and error log collection settings.
-   The number of log collection directories is unlimited. However, a file in a machine group cannot be referenced in the Logtail configuration of multiple Logstores \(it is not allowed no matter the file is explicitly specified or fuzzy matched\). Otherwise, the collection fails. If the log collection fails, you must modify the configuration in the Log Service console.
-   Currently, only EmptyDirVolume logs can be collected.


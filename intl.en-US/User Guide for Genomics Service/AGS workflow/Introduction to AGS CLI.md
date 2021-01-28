# Introduction to AGS CLI

Alibaba Cloud Genomics Compute Service \(AGS\) CLI is a generic command-line interface \(CLI\) for genomic compute services of Alibaba Cloud. AGS CLI integrates the features of Argo and provides support for commands of other Alibaba Cloud service add-ons.

## Download and install AGS CLI

By default, AGS CLI uses Alibaba Cloud AccessKey pairs to access other Alibaba Cloud services. AGS CLI uses Log Service to collect pod logs. To use Log Service in a cluster of Container Service for Kubernetes \(ACK\), you must enable Log Service when you create the ACK cluster.

Run the following command to download AGS CLI and make the downloaded file executable.

```
wget http://ags-hub.oss-cn-hangzhou.aliyuncs.com/ags-linux && chmod +x ags-linux && mv ags-linux  /usr/local/bin/ags 
```

**Note:**

You can run the `ags config init` to enter the required information in AGS CLI. By default, after AGS CLI is initialized, the configuration files are stored in the ~/.ags/config directory. You can run the `ags config show` command to display the configuration. In the configuration, AccessKeySecret is encrypted.

If you want to use Log Service to collect logs, you must configure ags config. For security purposes, we recommend that you create a separate AccessKey pair for AGS CLI and grant the permissions to access Log Service.

If you use a managed Kubernetes cluster, use kubectl to connect to the cluster and run the following command on Cloud Shell to use AGS CLI. For more information, see [Use kubectl to connect to an ACK cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster management/Access clusters/Use kubectl to connect to an ACK cluster.md).

```
wget http://ags-hub.oss-cn-hangzhou.aliyuncs.com/ags-linux && chmod +x ags-linux && mv ags-linux  /usr/local/bin/ags
```

## Features of AGS CLI

-   Fully compatible with Argo commands.
-   Allows you to query pod logs in Log Service after pods are deleted.
-   Allows you to use kubectl commands to manage clusters.
-   Supports the install and uninstall commands. You can quickly install and uninstall tools that are required by AGS CLI.
-   Allows you to run the get workflow command to query the resource usage of a workflow.
-   Supports special characters such as underscores \(\_\) in YAML templates.
-   Allows you to use the securityContext feature for security settings.
-   Synchronizes the pending and fails states between pods and workflows.
-   Allows you to enable automatic retry by using YAML files.
-   Allows you to retry a workflow from a failed step.
-   Supports the Elastic Container Instance \(ECI\) serverless Kubernetes architecture.

## AGS CLI overview

```
[root@iZwz92q9h36kv8posr0i6uZ ~]# ags
ags is the command line interface to Alibaba Cloud Genomics Compute Service

Usage:
 ags [flags]
 ags [command]

Available Commands:
 completion  output shell completion code for the specified shell (bash or zsh)
 config      setup ags client necessary info
 delete      delete a workflow and its associated pods
 get         display details about a workflow
 help        Help about any command
 install     install ags
 kubectl     kubectl command
 lint        validate a file or directory of workflow manifests
 list        list workflows
 logs        view logs of a workflow
 resubmit    resubmit a workflow
 resume      resume a workflow
 retry       retry a workflow
 submit      submit a workflow
 suspend     suspend a workflow
 terminate   terminate a workflow
 uninstall   uninstall ags
 version     Print version information
 wait        waits for a workflow to complete
 watch       watch a workflow until it completes

Flags:
     --as string                      Username to impersonate for the operation
     --as-group stringArray           Group to impersonate for the operation, this flag can be repeated to specify multiple groups.
     --certificate-authority string   Path to a cert file for the certificate authority
     --client-certificate string      Path to a client certificate file for TLS
     --client-key string              Path to a client key file for TLS
     --cluster string                 The name of the kubeconfig cluster to use
     --context string                 The name of the kubeconfig context to use
 -h, --help                           help for ags
     --insecure-skip-tls-verify       If true, the server's certificate will not be checked for validity. This will make your HTTPS connections insecure
     --kubeconfig string              Path to a kube config. Only required if out-of-cluster
 -n, --namespace string               If present, the namespace scope for this CLI request
     --password string                Password for basic authentication to the API server
     --request-timeout string         The length of time to wait before giving up on a single server request. Non-zero values should contain a corresponding time unit (e.g. 1s, 2m, 3h). A value of zero means don't timeout requests. (default "0")
     --server string                  The address and port of the Kubernetes API server
     --token string                   Bearer token for authentication to the API server
     --user string                    The name of the kubeconfig user to use
     --username string                Username for basic authentication to the API server

Use "ags [command] --help" for more information about a command.
```

## Install/Uninstall

You can run the ags install command to install tools that are required by AGS CLI. You do not need to manually install these tools.

You can run the ags uninstall command to uninstall all tools that are installed in AGS CLI.


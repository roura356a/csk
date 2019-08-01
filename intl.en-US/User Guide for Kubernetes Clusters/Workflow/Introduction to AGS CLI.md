# Introduction to AGS CLI {#concept_525487 .concept}

This topic describes the features of Alibaba Cloud Genomics Compute Service \(AGS\) and how to download and configure AGS. By default, AGS calls Alibaba Cloud services by using Alibaba Cloud access keys. AGS works with Log Service to collect pod logs. To use this function, you must select to enable Log Service when you create a cluster.

## Features {#section_daj_tr4_syf .section}

-   Fully compatible to argo commands
-   Works with Log Service, allowing you to view logs after a pod is deleted
-   Compatible to kubectl commands, allowing you to manage clusters by using kubectl
-   Provides install and uninstall commands, allowing you to install or uninstall resources as needed

    **Note:** 

    -   You can run the ags install command to install resources.
    -   You can run the ags uninstall command to uninstall resources.
-   Provides the get workflow command, allowing you to view the resource usage
-   Provides YAML templates that allow special characters such as underscores \(\_\)
-   Provides security contexts
-   Synchronizes pod status \(such as pending and failed\) with workflows
-   Uses YAML templates to customize retries
-   Retries an entire workflow when the workflow fails at any point
-   Supports ECI Serverless Kubernetes architecture

## Download and installation {#section_n3j_n7q_hj2 .section}

To install AGS and configure relevant permissions, run the following command:

``` {#codeblock_iyy_tot_6m0}
wget http://ags-hub.oss-cn-hangzhou.aliyuncs.com/ags-linux && chmod +x ags-linux && mv ags-linux  /usr/local/bin/ags 
```

**Note:** 

-   You can run the ags config init command to enter required information in the CLI. After the system is initialized, the configuration files are saved to the ~/.ags/config file. You can run theags config show command to display the configured information. In the configured information, the AccessKeySecret is encrypted.
-   To collect logs by using Log Service, you must first configure `ags config`. We recommend that you create an access key for the CLI and grant relevant permissions for Log Service.

If you are using a managed Kubernetes cluster, you can [connect to the Kubernetes cluster by using kubectl](intl.en-US//Connect to a Kubernetes cluster by using kubectl.md#) and run the following command to use AGS through Cloud Shell:

``` {#codeblock_xjc_am2_nfx}
wgethttp://ags-hub.oss-cn-hangzhou.aliyuncs.com/ags-linux&& chmod +x ags-linux && mv ags-linux  /usr/local/bin/ags
```

## Available commands in AGS CLI {#section_f6e_0pz_p1l .section}

The available commands in AGS CLI are as follows:

``` {#codeblock_lcd_kiz_fx6}
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


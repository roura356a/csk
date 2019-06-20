# AGS命令行帮助 {#concept_525487 .concept}

AGS 是阿里云基因服务的通用命令行工具， 目前主要集成 argo 功能并且适配阿里云各个产品的 addon功能命令。

## AGS 下载和安装 {#section_bs7_2zj_w60 .section}

AGS 默认使用阿里云ak调用各个服务，目前集成了阿里云日志服务用于收集Pod日志。使用此功能请在集群创建的时候选择开启日志服务。

执行如下命令，下载 AGS 工具并配置运行权限。

``` {#codeblock_5nm_wnj_dfm}
wget http://ags-hub.oss-cn-hangzhou.aliyuncs.com/ags-linux && chmod +x ags-linux && mv ags-linux  /usr/local/bin/ags 
```

**说明：** 

`ags config init`根据交互式命令行输入CLI需要的信息，初始化完成后，配置文件会被默认存储到~/.ags/config文件中。可以通过`ags config show`展示配置好的信息。其中AccessKeySecret会被加密存储。

如果您需要使用日志采集功能，则需要配置ags config。另外为了安全考虑，您可以给CLI单独创建一个ak，赋予日志服务权限即可。

## AGS 功能特性 {#section_daj_tr4_syf .section}

-   完全兼容argo 命令
-   Pod被删除后后从阿里云日志服务拉取日志查看功能
-   集成kubectl 命令，用户可以直接使用kubectl命令操作集群
-   提供install uninstall 命令，一键安装或者卸载所需资源
-   get workflow 命令提供资源使用量查看功能
-   Yaml 模板允许下划线等特殊字符
-   securityContext 安全支持
-   Pod pending/fails 状态与workflow 状态同步
-   Yaml定义自动retry功能
-   基于最近失败断点retry整个workflow
-   支持 ECI Serveless Kubernetes 架构

## AGS 基本概览 {#section_f6e_0pz_p1l .section}

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

## Install/Uninstall {#section_xil_8y9_o2d .section}

您可以通过ags install 命令，一键安装AGS所需资源，无需手动安装。

也可以通过ags uninstall命令， 一键卸载AGS所有资源。


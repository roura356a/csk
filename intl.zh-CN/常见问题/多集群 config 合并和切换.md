# 多集群 config 合并和切换 {#concept_960510 .concept}

本文为您提供了多集群 config 合并和切换的方法。

当前您可以在容器服务控制台集群详情页面，获取到当前登录用户对指定集群的 kubeconfig 访问凭证，或者通过调用 [openapi](https://yuque.antfin-inc.com/docs/share/7f991f9c-6ad2-4a72-a7c5-ac487667bafb) 的方式获取指定 config内容，当您的阿里云账号具有多个集群的访问权限时，可以通过如下命令合并多个集群的 kubeconfig 内容合并到一个统一的凭证文件中。

``` {#codeblock_08v_5n2_s92}
KUBECONFIG=file1:file2:file3 kubectl config view --merge --flatten > ~/.kube/all-config
export KUBECONFIG=~/.kube/all-config
```

-   执行如下命令，查看所有的可使用的 context 上下文。

    ``` {#codeblock_ofl_z11_ddj}
    kubectl config get-contexts
    ```

-   执行如下命令，查看 config 配置的帮助信息。

    ``` {#codeblock_uej_fwb_r89}
    kubectl config --help
    ```

-   执行如下命令，切换 context 上下文配置。

    ``` {#codeblock_gya_isd_kg1}
    kubectl config use-context {your-contexts}
    ```


更多 kubeconfig 相关使用介绍，请参见[官方使用文档](https://k8smeetup.github.io/docs/tasks/access-application-cluster/authenticate-across-clusters-kubeconfig/)。


# How do I combine kubeconfig files of multiple clusters? {#concept_960510 .concept}

If your Alibaba Cloud account has access to multiple clusters, you can run the following command to combine kubeconfig files of different clusters into one credential file:

``` {#codeblock_08v_5n2_s92}
KUBECONFIG=file1:file2:file3 kubectl config view --merge --flatten > ~/.kube/all-config
export KUBECONFIG=~/.kube/all-config
```

-   Run the following command to view the available contexts:

    ``` {#codeblock_ofl_z11_ddj}
    
    kubectl config get-contexts
    ```

-   Run the following command to view the help information:

    ``` {#codeblock_uej_fwb_r89}
    
    kubectl config --help
    ```

-   Run the following command to change the contexts:

    ``` {#codeblock_gya_isd_kg1}
    
    kubectl config use-context {your-contexts}
    ```


For more information about kubeconfig files, see [Configure access to multiple clusters](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/).

**Note:** You can obtain kubeconfig access credentials of the current user on the cluster list page of the Container Service console. You can also call an [API](../../../../reseller.en-US/Developer Guide/Cluster API list/Obtain the cluster kubeconfig file.md#) action to obtain a specific kubeconfig file.


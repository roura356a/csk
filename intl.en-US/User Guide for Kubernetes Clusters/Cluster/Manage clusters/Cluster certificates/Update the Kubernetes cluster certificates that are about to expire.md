# Update the Kubernetes cluster certificates that are about to expire

This topic describes how to update the Kubernetes cluster certificates that are about to expire. You can use multiple methods to update the cluster certificates. For example, you can update the cluster certificates in the Container Service for Kubernetes console. You can run a single command to update all the certificates. You can also run different commands to separately update the certificates of the master and worker nodes.

## Prerequisites

-   A Kubernetes cluster is created. For more information, see [Create a Kubernetes cluster](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Create Kubernetes clusters/Create a dedicated Kubernetes cluster.md).
-   The Kubernetes cluster is connected based on kubectl. For more information, see [Connect to a Kubernetes cluster by using kubectl](/intl.en-US/User Guide for Kubernetes Clusters/Cluster/Access clusters/Connect to ACK clusters by using kubectl.md).

## Run a command to update all certificates

Log on to a master node. In the command-line interface \(CLI\), run the following command:

```
curl http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/cert-update/renew.sh | bash
```

Response:

1.  In the CLI, run the following command to view the status of the master nodes and worker nodes:

    ```
    kubectl get nodes
    ```

    ![nodes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/2635359951/p21600.png)

2.  In the CLI, run the following command. The response shows that the COMPLETIONS parameter value of each master node is 1, and the COMPLETIONS parameter value of each worker node indicates the number of worker nodes in the cluster. In this case, all certificates are updated.

    ```
    kubectl get job –nkube-system
    ```

    ![nodes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0861987261/p21601.png)


## Manually update the certificates of each master node

1.  Copy and paste the following code into any path to create a job-master.yml file:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: ${jobname}
      namespace: kube-system
    spec:
      backoffLimit: 0
      completions: 1
      parallelism: 1
      template:
        spec:
          activeDeadlineSeconds: 3600
          affinity:
            nodeAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
                nodeSelectorTerms:
                - matchExpressions:
                  - key: kubernetes.io/hostname
                    operator: In
                    values:
                    - ${hostname}
          containers:
          - command:
            - /renew/upgrade-k8s.sh
            - --role
            - master
            image: registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0
            imagePullPolicy: Always
            name: ${jobname}
            securityContext:
              privileged: true
            volumeMounts:
            - mountPath: /alicoud-k8s-host
              name: ${jobname}       
          hostNetwork: true
          hostPID: true
          restartPolicy: Never
          schedulerName: default-scheduler
          securityContext: {}
          tolerations:
          - effect: NoSchedule
            key: node-role.kubernetes.io/master
          volumes:
          - hostPath:
              path: /
              type: Directory
            name: ${jobname}
    ```

2.  Obtain the number of master nodes in the cluster and the hostname of each master node.

    -   Method 1:

        In the CLI, run the following command:

        ```
        kubectl get nodes
        ```

        ![nodes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3635359951/p21548.png)

    -   Method 2:

        1.  Log on to the [Container Service for Kubernetes console](https://cs.console.aliyun.com).
        2.  In the left-side navigation pane, click **Clusters**.
        3.  On the Clusters page, click the name of the cluster that you want to manage, or click **Details** in the Actions column of the cluster.
        4.  The Cluster Information page appears. In the left-side navigation pane, click **Nodes**. On the **Nodes** page, check the number of master nodes, and the name, IP address, and instance ID of each node.
3.  In the CLI, run the following command to set the $\{jobname\} and $\{hostname\} variables in the job-master.yml file:

    ```
    sed 's/${jobname}/cert-job-2/g; s/${hostname}/hostname/g' job-master.yml > job-master2.yml
    ```

    where,

    -   $\{jobname\} is the job and pod name. In this example, this variable is set to cert-job-2.
    -   $\{hostname\} is the master name. In this example, hostname is set to a master name that is returned in Step 2.
4.  In the CLI, run the following command to create a job:

    ```
    kubectl create -f job-master2.yml
    ```

5.  In the CLI, run the following command to view the job status. When the COMPLETIONS parameter value is 1, the certificates of this master node have been updated.

    ```
    kubectl get job –nkube-system
    ```

6.  Repeat Steps 3 to 5 to update the certificates of the other master nodes in the cluster.

    ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0861987261/p21551.png)


## Manually update worker node certificates

1.  Copy and paste the following code into any path to create a job-node.yml file:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: ${jobname}
      namespace: kube-system
    spec:
      backoffLimit: 0
      completions: ${nodesize}
      parallelism: ${nodesize}
      template:
        spec:
          activeDeadlineSeconds: 3600
          affinity:
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchExpressions:
                  - key: job-name
                    operator: In
                    values:
                    - ${jobname}
                topologyKey: kubernetes.io/hostname
          containers:
          - command:
            - /renew/upgrade-k8s.sh
            - --role
            - node
            - --rootkey
            - ${key}
            image: registry.cn-hangzhou.aliyuncs.com/acs/cert-rotate:v1.0.0
            imagePullPolicy: Always
            name: ${jobname}
            securityContext:
              privileged: true
            volumeMounts:
            - mountPath: /alicoud-k8s-host
              name: ${jobname}
          hostNetwork: true
          hostPID: true
          restartPolicy: Never
          schedulerName: default-scheduler
          securityContext: {}
          volumes:
          - hostPath:
              path: /
              type: Directory
            name: ${jobname}
    ```

    **Note:** If a worker node has a taint, add `tolerations` for the taint in the job-node.yml file. You must add the following code between `securityContext: {}` and `volumes:`. If the number of worker nodes that have taints is n, you must add the following code n times\).

    ```
          tolerations:
          - effect: NoSchedule
            key: ${key}
            operator: Equal
            value: ${value}
    ```

    To obtain the values of $\{name\} and $\{value\}, perform the following steps:

    1.  Copy and paste the following code into any path to create a taint.tml file:

        ```
        {{printf "%-50s %-12s\n" "Node" "Taint"}}
        {{- range .items}}
            {{- if $taint := (index .spec "taints") }}
                {{- .metadata.name }}{{ "\t" }}
                {{- range $taint }}
                    {{- .key }}={{ .value }}:{{ .effect }}{{ "\t" }}
                {{- end }}
                {{- "\n" }}
            {{- end}}
        {{- end}}
        ```

    2.  Run the following command to view the values of $\{name\} and $\{value\} for the worker nodes that have taints:

        ```
        kubectl get nodes -o go-template-file="taint.tml"
        ```

        ![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/3635359951/p21570.png)

2.  In the CLI, run the following command to obtain the cluster CA key:

    ```
    sed '1d' /etc/kubernetes/pki/ca.key | base64 -w 0
    ```

3.  In the CLI, run the following command to set the $\{jobname\}, $\{nodesize\}, and $\{key\} variables in the job-node.yml file:

    ```
    sed 's/${jobname}/cert-node-2/g; s/${nodesize}/nodesize/g; s/${key}/key/g' job-node.yml > job-node2.yml
    ```

    where,

    -   $\{jobname\} is the job and pod name. In this example, this variable is set to cert-node-2.
    -   $\{nodesize\} is the number of worker nodes. For more information about how to obtain this value, see Step 1 in [Manually update worker node certificates](#section_f3i_kk1_7dz). In this example, the nodesize variable is replaced with the number of the worker nodes in the cluster.
    -   $\{key\} is the cluster CA key. In this example, the key variable is replaced with the CA key that is returned in Step 2 of [Manually update worker node certificates](#section_f3i_kk1_7dz).
4.  In the CLI, run the following command to create a job:

    ```
    kubectl create –f job-node2.yml
    ```

5.  Run the following command to view the job status. When the COMPLETIONS parameter value is equal to the number of the cluster worker nodes, all certificates have been updated.

    ```
    kubectl get job –nkube-system
    ```

    ![nodes](https://help-static-aliyun-doc.aliyuncs.com/assets/img/en-US/0861987261/p21565.png)



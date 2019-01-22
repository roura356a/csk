# Update the Kubernetes cluster certificates that are about to expire {#concept_jnx_v3b_rfb .concept}

This topic describes how to update the Kubernetes cluster certificates that are about to expire. You can use one of three methods to update the cluster certificates. You can update the cluster certificates in the Container Service console, update all the certificates by running a single command, or update Master and Worker node certificates separately by running different commands.

## Prerequisites {#section_d1t_x3b_rfb .section}

-   You have created a Kubernetes cluster. For more information, see [Create a Kubernetes cluster](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Create a Kubernetes cluster.md#).
-   You have connected to the Kubernetes cluster through kubectl. For more information, see [Connect to a Kubernetes cluster by using kubectl](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Connect to a Kubernetes cluster by using kubectl.md#).

## Updates all certificates through the Container Service console {#section_ywp_1qg_mgb .section}

In the Container Service console, click the **Update Certificate** prompt of the target cluster. For more information, see [Update the Kubernetes cluster certificates that are about to expire](../../../../../reseller.en-US/User Guide/Kubernetes cluster/Cluster management/Update the Kubernetes cluster certificates that are about to expire.md#).

## Run a command to update all certificates {#section_sz4_lyh_rfb .section}

Log on to a Master node and run the following command:

```
$ curl http://aliacs-k8s-cn-hangzhou.oss-cn-hangzhou.aliyuncs.com/public/cert-update/renew.sh | bash
```

**Verify the results**

1.  Run the following command to view the status of Master nodes and Worker nodes:

    ```
    $ kubectl get nodes
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/154812342821600_en-US.png)

2.  Run the following command. When the value of the SUCCESSFUL parameter of each Master node is 1, and the value of the SUCCESSFUL parameter of each Worker node meets the number of cluster Worker nodes, all certificates are updated.

    ```
    $ kubectl get job –nkube-system
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/154812342821601_en-US.png)


## Manually update the certificates of each Master node {#section_pdw_cjb_rfb .section}

1.  Copy the following code and paste it into any path to create a job-master.yml file:

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

2.  Obtain the number of Master nodes in the cluster and the hostname of each Master node.
    -   Method 1

        Run the following commands:

        ```
        $ kubectl get nodes
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/154812342821548_en-US.png)

    -   Method 2

        1.  Log on to the [Container Service console](https://partners-intl.console.aliyun.com/#/cs).
        2.  In the left-side navigation pane under Kubernetes, click **Clusters**.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/154812342821549_en-US.png)

        3.  Click the target cluster name, and then click **Node List** in the left-side navigation pane to view the number of Master nodes and the hostname of each Master node.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/154812342821550_en-US.png)

3.  Run the following command to specify the $\{jobname\} and $\{hostname\} variables in the job-master.yml file:

    ```
    $ sed 's/${jobname}/cert-job-2/g; s/${hostname}/hostname/g' job-master.yml > job-master2.yml
    ```

    In this code line:

    -   $\{jobname\} is the Job and pod name. In this example, this variable is set to cert-job-2.
    -   $\{hostname\} is the Master name. In this example, hostname is set to a Master name obtained in step 2.
4.  Run the following command to create a Job:

    ```
    $ kubectl create -f job-master2.yml
    ```

5.  Run the following command to view the Job status. When the value of the SUCCESSFUL parameter is 1, the certificates of this Master node have been updated.

    ```
    $ kubectl get job –nkube-system
    ```

6.  Repeat step 3 to step 5 to update the certificates of the remaining Master nodes in the cluster.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/154812342821551_en-US.png)


## Manually update Worker node certificates {#section_k4z_skb_rfb .section}

1.  Copy the following code and paste it into any path to create a job-node.yml file:

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

    **Note:** If a Worker node has a taint, you need to add `tolerations` for the taint in the job-node.yml file. More specifcially, you need to add the following code between `securityContext: {}` and `volumes:` \(If the number of Worker nodes that have taints is n, you need to add the following code n times\):

    ```
          tolerations:
          - effect: NoSchedule
            key: ${key}
            operator: Equal
            value: ${value}
    ```

    The method to obtain $\{name\} and $\{value\} is as follows:

    1.  Copy the following code and paste it into any path to create a taint.tml file:

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

    2.  Run the following command to view the values of $\{name\} and $\{value\} for the Worker nodes that have taints:

        ```
        $ kubectl get nodes -o go-template-file="taint.tml"
        ```

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/154812342821570_en-US.png)

2.  Run the following command to obtain the cluster CAKey:

    ```
    $ sed '1d' /etc/kubernetes/pki/ca.key | base64 -w 0
    ```

3.  Run the following command to specify the $\{jobname\}, $\{nodesize\}, and $\{key\} variables in the job-node.yml file:

    ```
    $ sed 's/${jobname}/cert-node-2/g; s/${nodesize}/nodesize/g; s/${key}/key/g' job-node.yml > job-node2.yml
    ```

    In this code line:

    -   $\{jobname\} is the Job and pod name. In this example, this variable is set to cert-node-2.
    -   $\{nodesize\} is the number of Worker nodes. For how to obtain this value, see step 2 in [Manually update the certificates of each Master node](#section_pdw_cjb_rfb). In this example, the nodesize variable is replaced with the number of the Worker nodes in the cluster.
    -   $\{key\} is the cluster CAKey. In this example, the key variable is replaced with the CAKey obtained in step 3 of [Manually update Worker node certificates](reseller.en-US/Best Practices/Cluster/Update the Kubernetes cluster certificates that are about to expire.md#section_k4z_skb_rfb).
4.  Run the following command to create a Job:

    ```
    $ kubectl create –f job-node2.yml
    ```

5.  Run the following command to view the Job status. When the value of the SUCCESSFUL parameter is equal to the number of the cluster Worker nodes, all certificates have been updated.

    ```
    $ kubectl get job –nkube-system
    ```

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/41643/154812342821565_en-US.png)



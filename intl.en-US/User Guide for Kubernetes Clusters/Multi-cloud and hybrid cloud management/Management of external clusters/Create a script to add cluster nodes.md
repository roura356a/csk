---
keyword: [add nodes, create a script to add nodes]
---

# Create a script to add cluster nodes

To add nodes to a hybrid cluster, you must consider how the self-managed Kubernetes cluster is created. For example, the cluster may be created by using kubeadm, Kubernetes binaries, or Rancher. This topic describes how to create a script to add nodes to a hybrid cluster.

The operations before [Step 5](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a hybrid cluster.md) in the Create a hybrid cluster topic are completed.

## Step 1: Create a script to add cluster nodes

The following code of kubelet configuration is provided as an example:

```
cat >/usr/lib/systemd/system/kubelet.service <<EOF
[Unit]
Description=Kubernetes Kubelet
After=docker.service
Requires=docker.service
[Service]
ExecStart=/data0/kubernetes/bin/kubelet \\
  --node-ip=${ALIBABA_CLOUD_NODE_NAME} \\
  --hostname-override=${ALIBABA_CLOUD_NODE_NAME} \\
  --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf \\
  --config=/var/lib/kubelet/config.yaml \\
  --kubeconfig=/etc/kubernetes/kubelet.conf \\
  --cert-dir=/etc/kubernetes/pki/ \\
  --cni-bin-dir=/opt/cni/bin \\
  --cni-cache-dir=/opt/cni/cache \\
  --cni-conf-dir=/etc/cni/net.d \\
  --logtostderr=false \\
  --log-dir=/var/log/kubernetes/logs \\
  --log-file=/var/log/kubernetes/logs/kubelet.log \\
  --node-labels=${ALIBABA_CLOUD_LABELS} \\
  --root-dir=/var/lib/kubelet \\
  --provider-id=${ALIBABA_CLOUD_PROVIDE_ID} \\
  --register-with-taints=${ALIBABA_CLOUD_TAINTS} \\
  --v=4
Restart=on-failure
RestartSec=5
[Install]
WantedBy=multi-user.target
EOF
```

When you write the script, you must use the system environment variables that are provided by the external cluster registered in the Container Service for Kubernetes \(ACK\) console. The following table lists the required system environment variables.

|System environment variable|Description|Example|
|---------------------------|-----------|-------|
|ALIBABA\_CLOUD\_PROVIDE\_ID|You must set this variable in the script. Otherwise, errors may occur during cluster management.|`ALIBABA_CLOUD_PROVIDE_ID=cn-shenzhen.i-wz92ewt14n9wx9mol2cd`|
|ALIBABA\_CLOUD\_NODE\_NAME|You must set this variable in the script. Otherwise, nodes in the node pool may have abnormal states.|`ALIBABA_CLOUD_NODE_NAME=cn-shenzhen.192.168.1.113`|
|ALIBABA\_CLOUD\_LABELS|You must set this variable in the script. Otherwise, errors may occur during node pool management and workload scheduling between cloud and on-premises nodes.|`ALIBABA_CLOUD_LABELS=alibabacloud.com/nodepool-id=np0e2031e952c4492bab32f512ce1422f6,ack.aliyun.com=cc3df6d939b0d4463b493b82d0d670c66,alibabacloud.com/instance-id=i-wz960ockeekr3dok06kr,alibabacloiud.com/external=true,workload=cpu`The workload=cpu label is a custom label defined in the node pool configuration. Other labels are system labels. |
|ALIBABA\_CLOUD\_TAINTS|You must set this variable in the script. Otherwise, the taints that are added to the node pool do not take effect.|`ALIBABA_CLOUD_TAINTS=workload=ack:NoSchedule`|

## Step 2: Save the script

Save the script to an HTTP file server, such as an Object Storage Service \(OSS\) bucket. The `https://kubelet-****.oss-ap-southeast-3-internal.aliyuncs.com/attachnode.sh` address is used as an example.

## Step 3: Use the script

1.  Register the on-premises Kubernetes cluster in the ACK console. For more information, see [Create a cluster registration proxy and register an on-premises cluster](/intl.en-US/User Guide for Kubernetes Clusters/Multi-cloud and hybrid cloud management/Management of external clusters/Create a cluster registration proxy and register an on-premises cluster.md).

    The cluster registration proxy automatically creates a ConfigMap named ack-agent-config in the kube-system namespace of the external cluster. The following code block shows the initial configuration of the ack-agent-config ConfigMap:

    ```
    apiVersion: v1
    data:
      addNodeScriptPath: ""
      enableNodepool: "true"
      isInit: "true"
    kind: ConfigMap
    metadata:
      name: ack-agent-config
      namespace: kube-system
    ```

2.  Add the `https://kubelet-****.oss-ap-southeast-3-internal.aliyuncs.com/attachnode.sh` address of the script to the `addNodeScriptPath` field and save the modification.

    The following YAML template is an example:

    ```
    apiVersion: v1
    data:
      addNodeScriptPath: https://kubelet-****.oss-ap-southeast-3-internal.aliyuncs.com/attachnode.sh 
      enableNodepool: "true"
      isInit: "true"
    kind: ConfigMap
    metadata:
      name: ack-agent-config
      namespace: kube-system
    ```



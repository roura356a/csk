---
keyword: [ACK专有集群, 提升etcd存储容量上限]
---

# 提升ACK专有集群的etcd存储容量上限

ACK专有集群（Dedicated Kubernetes Cluster）最初使用的etcd版本是社区v3.3.8，其支持的最大Backend DB Size是2 GB。当集群中写入的etcd存储超过2 GB时，则会出现无法写入etcd的情况。本文介绍如何通过升级etcd版本到v3.4.3来提升ACK专有集群的etcd存储容量上限。

-   ACK专有集群的etcd版本小于v3.4.3。
-   对etcd存储的需求大于2 GB。如果没有此需求，则可以不用升级。

etcd社区v3.4.3版本允许写入100 GB的etcd存储，可以解决etcd存储不足的问题。

1.  通过SSH依次登录到etcd所在的Master节点，确认当前etcd版本是v3.3.8。

2.  执行下面的shell脚本，脚本将会下载etcd-v3.4.3 binary并且启动新版本。

    **说明：**

    -   请对节点进行逐个升级，确保升级节点Ready后再升级其他节点的etcd。
    -   因为etcd具有高可用性，所以升级不会导致业务访问etcd异常。
    ```
    !/usr/bin/env bash
    ```

    ```
    etcdbin=http://aliacs-k8s-cn-hangzhou.oss.aliyuncs.com/etcd/etcd-v3.4.3/etcd
    etcdctlbin=http://aliacs-k8s-cn-hangzhou.oss.aliyuncs.com/etcd/etcd-v3.4.3/etcdctl
    
    function download(){
        wget -O etcd ${etcdbin}
        wget -O etcdctl ${etcdctlbin}
        chmod +x {etcd,etcdctl}
        mv etcd /usr/bin/etcd
        mv etcdctl /usr/bin/etcdctl
        etcd --version
    }
    
    function config() {
        ETCD_FILE=/lib/systemd/system/etcd.service
        sed -i "/ETCD_EXPERIMENTAL_BACKEND_BBOLT_FREELIST_TYPE/ d" ${ETCD_FILE}
        sed -i "/ETCD_QUOTA_BACKEND_BYTES/ d" ${ETCD_FILE}
        sed -i "/^\[Service\]/a\Environment=\"ETCD_EXPERIMENTAL_BACKEND_BBOLT_FREELIST_TYPE=map\"" ${ETCD_FILE}
        sed -i "/^\[Service\]/a\Environment=\"ETCD_QUOTA_BACKEND_BYTES=100000000000\"" ${ETCD_FILE}
        sed -i "s/initial-cluster-state new/initial-cluster-state existing/g" ${ETCD_FILE}
    
        systemctl daemon-reload
        systemctl restart etcd
    }
    
    download; config
    
    ENDPOINTS=`ps -eaf|grep etcd-servers|grep -v grep|awk -F "=" '{print $22}'|awk -F " " '{print $1}'`
    
    ETCDCTL_API=3 etcdctl \
            --endpoints=${ENDPOINTS}         \
            --cacert=/var/lib/etcd/cert/ca.pem         \
            --cert=/var/lib/etcd/cert/etcd-client.pem         \
            --key=/var/lib/etcd/cert/etcd-client-key.pem \
            member list
    ```

3.  执行以下命令，确认etcd进程启动成功。

    ```
    ps aux|grep etcd
    ```


检查和确认etcd的健康状况。

```
ETCDCTL_API=3 etcdctl --endpoints=${ENDPOINTS} \
      --cacert=/var/lib/etcd/cert/ca.pem \
      --cert=/var/lib/etcd/cert/etcd-client.pem \
      --key=/var/lib/etcd/cert/etcd-client-key.pem endpoint health
```

```
ENDPOINTS is healthy
```


# Increase the capacity of etcd for dedicated Kubernetes clusters

Dedicated Kubernetes clusters use etcd v3.3.8 by default, which functions as a backend store that supports up to 2 GB. A cluster can no longer write data to etcd if the data stored in etcd reaches 2 GB. This topic describes how to upgrade etcd to v3.4.3. This increases the capacity of etcd for dedicated Kubernetes clusters.

-   The etcd version is earlier than 3.4.3.
-   You want to store more than 2 GB of data in etcd. The upgrade is not mandatory if you do not require it.

The publicly available version of etcd 3.4.3 provides up to 100 GB of storage.

1.  Use SSH to log on to the master nodes where etcd is installed and verify that the current etcd version is 3.3.8.

2.  Run the following shell script to download etcd-v3.4.3 binary and start the new version:

    **Note:**

    -   Perform the upgrade on each node. Wait until the upgraded node is in the Ready state before you move on to the next one.
    -   etcd is highly available. You can still access etcd during the upgrade.
    ```
    ! /usr/bin/env bash
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

3.  Run the following command to check whether etcd is running:

    ```
    ps aux|grep etcd
    ```


Check the health status of etcd.

```
ETCDCTL_API=3 etcdctl --endpoints=${ENDPOINTS} \
      --cacert=/var/lib/etcd/cert/ca.pem \
      --cert=/var/lib/etcd/cert/etcd-client.pem \
      --key=/var/lib/etcd/cert/etcd-client-key.pem endpoint health
```

```
ENDPOINTS is healthy
```


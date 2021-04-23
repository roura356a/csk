---
keyword: [AEP, 非易失性存储卷]
---

# 使用AEP非易失性存储提升读写性能

在ACK环境下，声明化AEP非易失性存储作为块存储或文件系统方式访问，无任何应用侵入和修改，降低AEP使用复杂度，读写性能相比SSD提升2~10倍。本文主要介绍ACK中AEP（Apache Pass）非易失性存储卷在低延迟高速临时读写及数据密集型业务场景下的使用。

## 应用场景一：低延迟高速临时读写

低延迟高速临时读写包含以下场景：

-   CICD持续集成临时存储
-   小文件遍历搜索
-   高吞吐日志写
-   无服务器化临时文件读写

**FIO读写性能测试**

本文分别使用AEP PMEM-LVM类型和SSD类型卷创建应用，然后进行对比测试。



1.  部署YAML文件声明PMEM-LVM类型和SSD类型PVC。

2.  部署YAML文件挂载存储卷和运行FIO测试。

    -   通过`kubectl exec`进入测试容器内部，运行fio测试PMEM-LVM卷的写性能。

        ```
        mount | grep csi
        cd /data
        fio -filename=./testfile -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=4k -size=10G -numjobs=50 -runtime=180 -group_reporting -name=rand_100write_4k
        ```

        预期输出：

        ```
        write: IOPS=92.0k, BW=363MiB/s (381MB/s)(8812MiB/24262msec)
        lat (nsec): min=2054, max=95278, avg=10544.00, stdev=1697.17
        ```

    -   通过`kubectl exec`进入测试容器内部，运行fio测试SSD卷的写性能。

        ```
        cd /data
        fio -filename=./testfile -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=4k -size=10G -numjobs=50 -runtime=180 -group_reporting -name=rand_100write_4k
        ```

        预期输出：

        ```
        lat (usec): min=20, max=7168, avg=24.76, stdev=13.97
        write: IOPS=37.3k, BW=146MiB/s (153MB/s)(8744MiB/60001msec)
        ```

    可见AEP设备的PMEM-LVM性能是SSD本地盘设备的2~3倍。

    |卷类型|IOPS|Thoughput|
    |---|----|---------|
    |PMEM-LVM|92000|381 MB/s|
    |SSD|37000|153 MB/s|


## 应用场景二：数据密集型业务

本文通过使用云盘SSD及AEP方式部署MySQL数据库检测写性能。

**使用云盘（SSD）部署MySQL数据库**

1.  创建云盘SSD数据卷。

    1.  执行以下命令使用阿里云CSI创建云盘SSD数据卷。

        ```
        kubectl apply -f disk-mysql.yaml
        ```

        disk-mysql.yaml模板示例如下所示：

        ```
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: disk-mysql
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 100Gi
          storageClassName: alicloud-disk-topology
        ```

    2.  执行以下命令查看创建的SSD存储卷。

        ```
        kubectl get pvc disk-mysql
        ```

        预期输出：

        ```
        NAME         STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS             AGE
        disk-mysql   Bound    d-***       100Gi      RWO            alicloud-disk-topology   10h
        ```

2.  使用SSD存储卷创建MySQL数据库实例。

    1.  执行以下命令创建MySQL数据库实例。

        ```
        kubectl apply -f mysql-normal-ssd.yaml
        ```

        mysql-normal-ssd.yaml模板示例如下所示：

        ```
        apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
        kind: Deployment
        metadata:
          name: mysql-normal
          labels:
            app: wordpress
        spec:
          selector:
            matchLabels:
              app: wordpress
              tier: mysql
          strategy:
            type: Recreate
          template:
            metadata:
              labels:
                app: wordpress
                tier: mysql
            spec:
              hostNetwork: true
              containers:
              - image: mysql:5.6
                name: mysql
                env:
                - name: MYSQL_ROOT_PASSWORD
                  valueFrom:
                    secretKeyRef:
                      name: mysql-pass
                      key: password
                ports:
                - containerPort: 3306
                  name: mysql
                volumeMounts:
                - name: mysql
                  mountPath: /var/lib/mysql
              volumes:
                - name: mysql
                  persistentVolumeClaim:
                    claimName: disk-mysql
        ---
        apiVersion: v1
        kind: Secret
        metadata:
          name: mysql-pass
        type: Opaque
        data:
          username: YWRtaW4=
          password: YWRtaW4=
        ```

    2.  执行以下命令查看创建的MySQL数据库实例。

        ```
        kubectl get pod | grep mysql-normal
        ```

        预期输出：

        ```
        mysql-normal-***        1/1     Running   0          10h
        ```


**使用AEP创建MySQL数据库操作步骤如下。**

1.  创建PMEM-LVM存储卷。

    1.  执行以下命令使用阿里云CSI创建PMEM-LVM存储卷。

        ```
        kubectl apply -f csi-pmem-lvm.yaml
        ```

        csi-pmem-lvm.yaml模板示例如下所示：

        ```
        apiVersion: v1
        kind: PersistentVolumeClaim
        metadata:
          name: pmem-mysql
        spec:
          accessModes:
          - ReadWriteOnce
          resources:
            requests:
              storage: 100Gi
          storageClassName: csi-pmem-lvm
        ```

    2.  执行以下命令查看创建的PMEM-LVM存储卷。

        ```
        kubectl get pvc pmem-mysql
        ```

        预期输出：

        ```
        NAME         STATUS   VOLUME       CAPACITY   ACCESS MODES   STORAGECLASS          AGE
        pmem-mysql   Bound    d-***        100Gi      RWO            csi-pmem-lvm          10h
        ```

2.  在挂载的PMEM存储卷创建MySQL数据库。

    1.  执行以下命令在挂载的PMEM存储卷创建MySQL数据库。

        ```
        kubectl apply -f mysql-normal-pmem.yaml
        ```

        mysql-normal-pmem.yaml模板示例如下所示：

        ```
        apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
        kind: Deployment
        metadata:
          name: mysql-normal
          labels:
            app: wordpress
        spec:
          selector:
            matchLabels:
              app: wordpress
              tier: mysql
          strategy:
            type: Recreate
          template:
            metadata:
              labels:
                app: wordpress
                tier: mysql
            spec:
              hostNetwork: true
              nodeName: <NODE name of AEP worker node>
              containers:
              - image: mysql:5.6
                name: mysql
                env:
                - name: MYSQL_ROOT_PASSWORD
                  valueFrom:
                    secretKeyRef:
                      name: mysql-pass
                      key: password
                ports:
                - containerPort: 3306
                  name: mysql
                volumeMounts:
                - name: mysql
                  mountPath: /var/lib/mysql
              volumes:
                - name: mysql
                  persistentVolumeClaim:
                    claimName: pmem-mysql
        ---
        apiVersion: v1
        kind: Secret
        metadata:
          name: mysql-pass
        type: Opaque
        data:
          username: YWRtaW4=
          password: YWRtaW4=
        ```

    2.  执行以下命令查看创建的MySQL数据库实例。

        ```
        kubectl get pod | grep mysql-normal
        ```

        预期输出：

        ```
        mysql-normal-***        1/1     Running   0          10h
        ```


**数据库写性能测试**

在Pod中执行以下命令进行数据库性能压测。

```
sysbench /root/sysbench/point_select.lua run --db-driver=mysql --report-interval=1
--mysql-table-engine=innodb --oltp-table-size=10000 --oltp-tables-count=32 --oltp-test-mode=complex 
--time=100 --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root --mysql-password=admin 
--mysql-db=sbtest --oltp-read-only=on --oltp-simple-ranges=0 --oltp-sum-ranges=0 
--oltp-order-ranges=0 --oltp-distinct-ranges=0  --oltp-dist-type=uniform --warmup-time=300 
--max-time=30 --max-requests=0 --percentile=99 --num-threads=150
```

测试结果：

|卷类型|Insert吞吐量（次/秒）|
|---|--------------|
|SSD|49812|
|PMEM-LVM|122156|

可见相比于SSD，使用AEP非易失性存储的数据库写入性能约为其2.5倍。

**相关文档**  


[ACK支持AEP非易失性数据卷](/cn.zh-CN/Kubernetes集群用户指南/存储-CSI/AEP非易失性存储卷/ACK支持AEP非易失性数据卷.md)


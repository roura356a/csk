---
keyword: [AEP, non-volatile memory]
---

# Use AEP non-volatile memory to improve read and write performance

Container Service for Kubernetes \(ACK\) allows you to use Apache Pass \(AEP\) non-volatile memory as block storage or file systems without modifications to your applications. This makes it easy to use AEP and improves read and write performance by 2 to 10 times compared with solid-state drives \(SSDs\). This topic describes how to use AEP non-volatile memory as volumes in ACK clusters to meet the requirements of data-intensive workloads and scenarios that require low-latency and high-speed temporary storage.

## Scenario 1: Low-latency and high-speed temporary storage

The following scenarios require low-latency and high-speed temporary storage:

-   Temporary storage in continuous integration and continuous delivery \(CD/CD\)
-   Small file traversal
-   High-throughput logging
-   Temporary file reads and writes in serverless scenarios

**Use FIO to test read and write performance**

In this topic, two persistent volume claims \(PVCs\) are created based on AEP PMEM-LVM and SSD to test read and write performance.



1.  Deploy the following YAML file to create two PVCs based on PMEM-LVM and SSD:

2.  Deploy the following YAML file to mount the volumes to pods and run FIO tests:

    -   Use the `kubectl exec` command to run FIO tests inside containers to test the write performance of the PMEM-LVM volume:

        ```
        mount | grep csi
        cd /data
        fio -filename=./testfile -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=4k -size=10G -numjobs=50 -runtime=180 -group_reporting -name=rand_100write_4k
        ```

        Expected output:

        ```
        write: IOPS=92.0k, BW=363MiB/s (381MB/s)(8812MiB/24262msec)
        lat (nsec): min=2054, max=95278, avg=10544.00, stdev=1697.17
        ```

    -   Use the `kubectl exec` command to run FIO tests inside containers to test the write performance of the SSD volume:

        ```
        cd /data
        fio -filename=./testfile -direct=1 -iodepth 1 -thread -rw=randwrite -ioengine=psync -bs=4k -size=10G -numjobs=50 -runtime=180 -group_reporting -name=rand_100write_4k
        ```

        Expected output:

        ```
        lat (usec): min=20, max=7168, avg=24.76, stdev=13.97
        write: IOPS=37.3k, BW=146MiB/s (153MB/s)(8744MiB/60001msec)
        ```

    The output indicates that the read and write performance of the AEP device is two to three times higher than that of the SSD device.

    |Volume type|IOPS|Thoughput|
    |-----------|----|---------|
    |PMEM-LVM|92000|381 MB/s|
    |SSD|37000|153 MB/s|


## Scenario 2: Data-intensive workloads

In this topic, an SSD and an AEP device are used to deploy MySQL databases. Then, the database write performance is tested.

**Use an SSD to deploy a MySQL database**

1.  Create an SSD volume.

    1.  Run the following command to create an SSD volume by using the Container Storage Interface \(CSI\) driver provided by Alibaba Cloud:

        ```
        kubectl apply -f disk-mysql.yaml
        ```

        In this example, the following disk-mysql.yaml template is used:

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

    2.  Run the following command to query the SSD volume:

        ```
        kubectl get pvc disk-mysql
        ```

        Expected output:

        ```
        NAME         STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS             AGE
        disk-mysql   Bound    d-***       100Gi      RWO            alicloud-disk-topology   10h
        ```

2.  Create a MySQL instance based on the SSD volume.

    1.  Run the following command to create a MySQL instance:

        ```
        kubectl apply -f mysql-normal-ssd.yaml
        ```

        In this example, the following mysql-normal-ssd.yaml template is used:

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

    2.  Run the following command to query the pod that runs the MySQL instance:

        ```
        kubectl get pod | grep mysql-normal
        ```

        Expected output:

        ```
        mysql-normal-***        1/1     Running   0          10h
        ```


**Use AEP to deploy a MySQL database**

1.  Create a PMEM-LVM volume.

    1.  Run the following command to create a PMEM-LVM volume by using the CSI driver provided by Alibaba Cloud:

        ```
        kubectl apply -f csi-pmem-lvm.yaml
        ```

        In this example, the following csi-pmem-lvm.yaml template is used:

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

    2.  Run the following command to query the PMEM-LVM volume:

        ```
        kubectl get pvc pmem-mysql
        ```

        Expected output:

        ```
        NAME         STATUS   VOLUME       CAPACITY   ACCESS MODES   STORAGECLASS          AGE
        pmem-mysql   Bound    d-***        100Gi      RWO            csi-pmem-lvm          10h
        ```

2.  Create a MySQL instance based on the PMEM-LVM volume.

    1.  Run the following command to create a MySQL instance based on the PMEM-LVM volume:

        ```
        kubectl apply -f mysql-normal-pmem.yaml
        ```

        In this example, the following mysql-normal-pmem.yaml template is used:

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

    2.  Run the following command to query the pod that runs the MySQL instance:

        ```
        kubectl get pod | grep mysql-normal
        ```

        Expected output:

        ```
        mysql-normal-***        1/1     Running   0          10h
        ```


**Test database write performance**

Run the following command on the pod to test database performance:

```
sysbench /root/sysbench/point_select.lua run --db-driver=mysql --report-interval=1
--mysql-table-engine=innodb --oltp-table-size=10000 --oltp-tables-count=32 --oltp-test-mode=complex 
--time=100 --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=root --mysql-password=admin 
--mysql-db=sbtest --oltp-read-only=on --oltp-simple-ranges=0 --oltp-sum-ranges=0 
--oltp-order-ranges=0 --oltp-distinct-ranges=0  --oltp-dist-type=uniform --warmup-time=300 
--max-time=30 --max-requests=0 --percentile=99 --num-threads=150
```

Test result:

|Volume type|Insert IOPS|
|-----------|-----------|
|SSD|49812|
|PMEM-LVM|122156|

The result indicates that the database write performance is 2.5 times higher when the AEP device is used than when the SSD device is used.

**Related topics**  


[Use AEP in ACK clusters]()


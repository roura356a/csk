---
keyword: [AEP直接内存, 扩容内存数据库, Direct Memory]
---

# 使用AEP的直接内存访问方式部署Redis数据库

在ACK环境下，通过修改应用内存分配部分函数对PMEM（Persistent Memory）设备直接访问，可以实现接近DRAM访问的吞吐和时延。本文主要介绍如何在大容量内存场景下通过DRAM及PMEM方式部署内存数据库Redis。

在大容量内存场景下使用AEP直接内存访问（Direct Memory）方式部署内存数据库Redis的优势：

-   声明化AEP内存访问，降低AEP使用复杂度。
-   同样容量内存节省30%~50%的成本。
-   最小化应用修改。

Redis以直接内存访问方式使用AEP设备调用MMAP对PMEM的连续地址空间进行映射。此场景下需要使用特定的Redis版本，可参考本文示例获取Redis镜像。

## 通过DRAM部署Redis内存数据库

内存（DRAM）可以提供优异的访问性能，但掉电时数据容易丢失且价格昂贵，不益于大容量内存使用。

1.  执行以下命令部署Redis服务。

    ```
    kubectl apply -f redis-normal.yaml
    ```

    redis-normal.yaml模板示例如下所示：

    ```
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: redis-normal
      labels:
        app: redis-normal
    spec:
      selector:
        matchLabels:
          app: redis-normal
      template:
        metadata:
          labels:
            app: redis-normal
        spec:
          containers:
          - name: redis
            image: registry.cn-hangzhou.aliyuncs.com/plugins/redis:v1-normal
            imagePullPolicy: Always
          resources:
            requests:
              memory: 30Gi
            limits:
              memory: 30Gi
    ```

2.  执行以下命令查看Redis服务。

    ```
    kubectl get pod | grep redis-normal
    ```

    预期输出：

    ```
    redis-normal-***         1/1     Running   0          4d8h
    ```

3.  执行以下命令测试Redis写性能。

    ```
    LD_PRELOAD=/usr/local/lib/libmemkind.so redis-benchmark -d 102400 -t set -n 1000000 -r 1000000
    ```

    预期输出：

    ```
    1000000 requests completed in 13.03 seconds
    76751.86 requests per second
    ```

    从以上预期输出可以得出：通过DRAM部署的Redis内存数据库，每秒大约处理76751个请求。


## 通过PMEM部署Redis内存数据库

持久化内存Persistent Memory（PMEM）既兼顾非易失性存储，又接近DRAM的使用方式和速度。

1.  执行以下命令部署Redis内存数据库。

    ```
    kubectl apply -f redis-normal.yaml
    ```

    redis-normal.yaml的示例模板如下所示：

    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pmem-pvc-direct
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 60Gi
      storageClassName: csi-pmem-direct
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: redis-pmem-direct
      labels:
        app: redis-pmem-direct
    spec:
      selector:
        matchLabels:
          app: redis-pmem-direct
      template:
        metadata:
          labels:
            app: redis-pmem-direct
        spec:
          containers:
          - name: nginx
            image: registry.cn-hangzhou.aliyuncs.com/plugins/redis:v1-pmem
            imagePullPolicy: Always
            volumeMounts:
              - name: pmem-pvc
                mountPath: "/mnt/pmem"
          volumes:
            - name: pmem-pvc
              persistentVolumeClaim:
                claimName: pmem-pvc-direct
    ```

2.  执行以下命令查看部署的Redis。

    ```
    kubectl get pod | grep redis-pmem-direct
    ```

    预期输出：

    ```
    redis-pmem-direct-5b469546db-5tk2j   1/1     Running   0          4d9h
    ```

3.  执行以下命令测试Redis写性能。

    ```
    LD_PRELOAD=/usr/local/lib/libmemkind.so redis-benchmark -d 10240 -t set -n 1000000 -r 1000000
    ```

    预期输出：

    ```
    1000000 requests completed in 19.89 seconds
    50266.41 requests per second
    ```

    从以上预期输出可以得出：通过PMEM部署的Redis内存数据库，每秒大约处理50266个请求。

    相比DRAM部署Redis内存数据库的写性能，PMEM部署的Redis内存数据库的写性能降低约34%。



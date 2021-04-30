---
keyword: [AEP direct memory, expand in-memory databases, direct memory]
---

# Use AEP as direct memory to deploy a Redis database

Container Service for Kubernetes allows you to use persistent memory \(PMEM\) as direct memory by modifying the memory allocation functions. This offers high throughput and low latency that are comparable to dynamic random-access memory \(DRAM\). This topic describes how to use DRAM and PMEM to deploy Redis in-memory databases that require large memory capacity.

When you use AEP as direct memory to deploy Redis in-memory databases, the following benefits are provided:

-   Simplifies the use of AEP resources
-   Saves the memory cost by 30% to 50%
-   Minimizes application modifications

Redis uses AEP devices as direct memory and calls the MMAP function to map the consecutive address space of PMEM. This scenario requires specific Redis versions. You can find the Redis image address in the following examples.

## Use DRAM to deploy a Redis in-memory database

DRAM provides high-speed reads and writes but loses data when power is off. DRAM does not apply to large-scale scenarios due to its high costs.

1.  Run the following command to deploy a Redis instance:

    ```
    kubectl apply -f redis-normal.yaml
    ```

    In this example, the following redis-normal.yaml template is used:

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

2.  Run the following command to query the pod that runs the Redis instance:

    ```
    kubectl get pod | grep redis-normal
    ```

    Expected output:

    ```
    redis-normal-***         1/1     Running   0          4d8h
    ```

3.  Run the following command to test the write performance of Redis:

    ```
    LD_PRELOAD=/usr/local/lib/libmemkind.so redis-benchmark -d 102400 -t set -n 1000000 -r 1000000
    ```

    Expected output:

    ```
    1000000 requests completed in 13.03 seconds
    76751.86 requests per second
    ```

    The preceding output shows that the Redis in-memory database that is deployed with DRAM can process approximately 76,751 requests per second.


## Use PMEM to deploy a Redis in-memory database

PMEM provides non-volatile memory and has nearly the same access method and speed of DRAM.

1.  Run the following command to deploy a Redis instance:

    ```
    kubectl apply -f redis-normal.yaml
    ```

    In this example, the following redis-normal.yaml template is used:

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

2.  Run the following command to query the pod that runs the Redis instance:

    ```
    kubectl get pod | grep redis-pmem-direct
    ```

    Expected output:

    ```
    redis-pmem-direct-5b469546db-5tk2j   1/1     Running   0          4d9h
    ```

3.  Run the following command to test the write performance of Redis:

    ```
    LD_PRELOAD=/usr/local/lib/libmemkind.so redis-benchmark -d 10240 -t set -n 1000000 -r 1000000
    ```

    Expected output:

    ```
    1000000 requests completed in 19.89 seconds
    50266.41 requests per second
    ```

    The preceding output shows that the Redis in-memory database that is deployed with PMEM can process about 50,266 requests per second.

    Compared with the Redis in-memory database deployed with DRAM, the Redis in-memory database deployed with PMEM reduces the write performance by 34% but offers the same read performance and saves cost by 50%. ECS instances that use AEP devices, such as ecs.ebmre6p.26xlarge, have a maximum capacity of 1.5 TB PEM memory, which is five times the capacity of common in-memory databases.



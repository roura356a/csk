---
keyword: [elastic model training, Horovod, dynamically adjust the number of workers]
---

# Elastic training

Elastic model training integrates the Elastic Horovod feature and allows Horovod to dynamically adjust the number of workers for distributed training tasks. You can enable elastic training on a cluster of preemptible instances to make full use of idle compute resources and reduce the training cost. This topic describes how to deploy elastic training tasks and how to scale resources for training tasks.

-   The cloud-native AI component set is installed and the **elastic training** component \(**et-operator**\) is selected. For more information, see [Deploy cloud-native AI component set](/intl.en-US/Cloud-native AI user guide/Environment preparation/Install Cloud-native AI Component Set and access AI Dashboard.md).
-   Horovod is used as a distributed training framework.
-   The Arena client is installed. For more information, see [Install the latest version of Arena](/intl.en-US/Solutions/Deep learning solution/Preparations/Install the latest version of Arena.md).

For traditional distributed deep learning tasks, the number of workers cannot be dynamically adjusted after a training task is submitted.

Model training is a key step in deep learning. The training of complex models can take a long time and require large amounts of computing power. Elastic training allows you to dynamically adjust the number of workers for model training tasks.

## Deploy an elastic training task

**Submit a training task**

Run the following command to submit a training task:

```
arena submit etjob \
    --name=elastic-training \
    --gpus=1 \
    --workers=3 \
    --max-workers=9 \
    --min-workers=1 \
    --image=registry.cn-hangzhou.aliyuncs.com/ai-samples/horovod:0.20.0-tf2.3.0-torch1.6.0-mxnet1.6.0.post0-py3.7-cuda10.1 \
    --working-dir=/examples \
    "horovodrun
    -np \$((\${workers}*\${gpus}))
    --min-np \$((\${minWorkers}*\${gpus}))
    --max-np \$((\${maxWorkers}*\${gpus}))
    --host-discovery-script /etc/edl/discover_hosts.sh
    python /examples/elastic/tensorflow2_mnist_elastic.py
    "
```

In this example, the `horovodrun` wrapper is used to run an elastic training task with Horovod. The np, max-np, and min-np parameters are required to run the task. Arena writes the parameter values to environment variables. You can specify the environment variables when you submit the task.

The following table describes the parameters.

|Parameter|Description|
|---------|-----------|
|--name|The name of the training task. The name must be globally unique.|
|--gpus|The number of GPUs per worker.|
|--max-workers|The maximum number of workers that run the training task.|
|--min-workers|The minimum number of workers that run the training task.|
|--image|The container image that is used to run the training task.|
|--working-dir|The directory where the command is executed.|
|--np|The number of workers during task execution.|
|--max-np|The maximum number of workers during task execution.|
|--min-np|The minimum number of workers during task execution.|
|--host-discovery-script|The host-discovery-script parameter specifies the path of the host discovery script that is created by the **et-operator** component in /etc/edl/discover\_hosts.sh.|

Expected output:

```
configmap/elastic-training-etjob created
configmap/elastic-training-etjob labeled
trainingjob.kai.alibabacloud.com/elastic-training created
INFO[0000] The Job elastic-training has been submitted successfully
INFO[0000] You can run `arena get elastic-training --type etjob` to check the job status
```

**Query the training task**

Run the following command to query the training task:

```
arena get elastic-training
```

Expected output:

```
Name:        elastic-training
Status:      RUNNING
Namespace:   default
Priority:    N/A
Trainer:     ETJOB
Duration:    13s

Instances:
  NAME                       STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
  ----                       ------   ---  --------  --------------  ----
  elastic-training-launcher  Running  13s  true      0               cn-huhehaote.192.168.0.173
  elastic-training-worker-0  Running  13s  false     1               cn-huhehaote.192.168.0.174
  elastic-training-worker-1  Running  13s  false     1               cn-huhehaote.192.168.0.174
```

**Query training logs**

Run the following command to query training logs:

```
arena logs elastic-training --tail 10
```

Expected output:

```
[0]<stdout>:Step #340    Loss: 0.047924
[1]<stdout>:Step #340    Loss: 0.116303
[0]<stdout>:Step #350    Loss: 0.068762
[1]<stdout>:Step #350    Loss: 0.040847
[0]<stdout>:Step #360    Loss: 0.057501
[1]<stdout>:Step #360    Loss: 0.111952
[0]<stdout>:Step #370    Loss: 0.085895
[1]<stdout>:Step #370    Loss: 0.075529
[0]<stdout>:Step #380    Loss: 0.063450
[1]<stdout>:Step #380    Loss: 0.054253
```

## Add workers for the training task

**Submit a scale-out task**

Run the following command to submit a scale-out task:

```
arena scaleout etjob --name="elastic-training" --count=1 --timeout=10m
```

-   --name: the name of the training task for which you want to add workers.
-   --count: the number of workers that you want to add for the training task.
-   --timeout: the timeout period of the scale-out operation.

If workers are not created before the timeout period ends, the scheduler rolls back the scale-out operation.

Expected output:

```
configmap/elastic-training-1609914643-scaleout created
configmap/elastic-training-1609914643-scaleout labeled
scaleout.kai.alibabacloud.com/elastic-training-1609914643 created
INFO[0003] The scaleout job elastic-training-1609914643 has been submitted successfully
```

**Query the training task**

Run the following command to query the training task:

```
arena get elastic-training
```

Expected output:

```
Name:        elastic-training
Status:      RUNNING
Namespace:   default
Priority:    N/A
Trainer:     ETJOB
Duration:    3m

Instances:
  NAME                       STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
  ----                       ------   ---  --------  --------------  ----
  elastic-training-launcher  Running  3m   true      0               cn-huhehaote.192.168.0.173
  elastic-training-worker-0  Running  3m   false     1               cn-huhehaote.192.168.0.174
  elastic-training-worker-1  Running  3m   false     1               cn-huhehaote.192.168.0.174
  elastic-training-worker-2  Running  1m   false     1               cn-huhehaote.192.168.0.173
```

The preceding output shows that a worker named `elastic-training-worker-2` is added to run the training task.

**Query training logs**

Run the following command to query training logs:

```
arena logs elastic-training --tail 10
```

Expected output:

```
[1]<stdout>:Step #1670    Loss: 0.131210
[2]<stdout>:Step #1680    Loss: 0.020876
[0]<stdout>:Step #1680    Loss: 0.030605
[1]<stdout>:Step #1680    Loss: 0.074515
[2]<stdout>:Step #1690    Loss: 0.029105
[0]<stdout>:Step #1690    Loss: 0.015216
[1]<stdout>:Step #1690    Loss: 0.022670
[0]<stdout>:Step #1700    Loss: 0.105407
[1]<stdout>:Step #1700    Loss: 0.037623
[2]<stdout>:Step #1700    Loss: 0.032874
```

The preceding output shows that three workers are running the task.

## Remove workers from the training task

**Submit a scale-in task**

Run the following command to submit a scale-in task:

```
arena scalein etjob --name="elastic-training" --count=1 --timeout=10m
```

-   --name: the name of the training task from which you want to remove workers.
-   --count: the number of workers that you want to remove from the training task.
-   --timeout: the timeout period of the scale-in operation.

Expected output:

```
configmap/elastic-training-1609914720-scalein created
configmap/elastic-training-1609914720-scalein labeled
scalein.kai.alibabacloud.com/elastic-training-1609914720 created
INFO[0002] The scalein job elastic-training-1609914720 has been submitted successfully
```

**Query the training task**

Run the following command to query the training task:

```
arena get elastic-training
```

Expected output:

```
Name:        elastic-training
Status:      RUNNING
Namespace:   default
Priority:    N/A
Trainer:     ETJOB
Duration:    3m

Instances:
  NAME                       STATUS   AGE  IS_CHIEF  GPU(Requested)  NODE
  ----                       ------   ---  --------  --------------  ----
  elastic-training-launcher  Running  3m   true      0               cn-huhehaote.192.168.0.173
  elastic-training-worker-0  Running  3m   false     1               cn-huhehaote.192.168.0.174
  elastic-training-worker-1  Running  3m   false     1               cn-huhehaote.192.168.0.174
```

The preceding output shows that the worker named `elastic-training-worker-2` is removed.

**Query training logs**

Run the following command to query training logs:

```
arena logs elastic-training --tail 10
```

Expected output:

```
[1]<stdout>:Step #2180    Loss: 0.001739
[0]<stdout>:Step #2180    Loss: 0.004853
[0]<stdout>:Step #2190    Loss: 0.000846
[1]<stdout>:Step #2190    Loss: 0.007900
[0]<stdout>:Step #2200    Loss: 0.039376
[1]<stdout>:Step #2200    Loss: 0.024672
[0]<stdout>:Step #2210    Loss: 0.012985
[1]<stdout>:Step #2210    Loss: 0.010956
[0]<stdout>:Step #2220    Loss: 0.009604
[1]<stdout>:Step #2220    Loss: 0.002531
```

The preceding output shows that only two workers are running the task.


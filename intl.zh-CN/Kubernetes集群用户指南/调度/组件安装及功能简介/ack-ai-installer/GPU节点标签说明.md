---
keyword: [共享GPU, GPU拓扑感知, GPU节点标签]
---

# GPU节点标签说明

安装ack-ai-installer组件之后，您可以为GPU节点打上一些特定标签，从而启用不同的功能，包括GPU共享调度、GPU显存隔离和GPU拓扑感知功能。本文介绍GPU节点的特定标签和GPU功能。

## 节点标签说明

如果需要使用共享GPU或GPU拓扑感知功能，需要为GPU节点打上一些特定标签。

**说明：** 节点切换互斥标签时，必须要保证该节点上申请扩展资源的Pod已经运行完成。例如，节点的标签需要由ack.node.gpu.schedule=share切换到ack.node.gpu.schedule=topology，那么必须保证该节点上申请了aliyun.com/gpu-mem资源的Pod全部运行完成后，才能切换。

|标签|扩展资源名称|说明|
|--|------|--|
|ack.node.gpu.schedule=share|aliyun.com/gpu-mem|在节点上开启GPU共享调度能力，不开启GPU显存隔离能力。|
|ack.node.gpu.schedule=cgpu|aliyun.com/gpu-mem|在节点上开启GPU共享调度能力，同时开启GPU显存隔离能力。|
|ack.node.gpu.schedule=topology|aliyun.com/gpu|在节点上开启GPU拓扑感知能力。|
|ack.node.gpu.schedule=default|nvidia.com/gpu|在节点使用默认GPU调度能力。|
|ack.node.gpu.placement=binpack|\\|在节点上为Pod选择GPU卡时，使用Binpack算法（仅对共享GPU生效）。|
|ack.node.gpu.placement=spread|\\|在节点上为Pod选择GPU卡时，使用Spread算法（仅对共享GPU生效）。|

## GPU共享调度和显存隔离

-   GPU共享调度指的是多个Pod可以共享同一节点的同一张GPU卡。例如有一个节点拥有两张GPU卡（GPU1和GPU2，显存都为15 GiB），某一时刻有两个Pod都在使用GPU1，此时可以称为Pod1和Pod2共享使用GPU1。
-   GPU显存隔离指的是当多个Pod共享同一张GPU卡时，每个Pod使用的显存进行了隔离限制。

    -   如下图左边部分所示，Pod1和Pod2申请的显存大小分别为2 GiB和3 GiB，但是每个Pod可使用的显存都是GPU1的显存（15 GiB），这种情况就是没有显存隔离。如果Pod没有设置显存隔离，那就存在Pod运行失败的风险。例如，某个Pod申请的显存为2 GiB，但是实际使用了15 GiB，将会导致共享GPU1的Pod都运行失败。
    -   如下图的右边部分所示，每个Pod使用的显存都是独占的，而且是被限制的。例如，某个Pod申请了2 GiB显存，那么也只能使用2 GiB显存。如果该Pod使用3 GiB显存，则程序会异常退出。
    ![显存隔离](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7486936061/p185643.png)


## GPU拓扑感知调度

如下图所示，某一个节点有4张GPU卡，分别为GPU1、GPU2、GPU3、GPU4。GPU1和GPU2两张卡在通信时，它们的通信带宽最大，而GPU1和GPU4在通信时，它们的通信带宽最小。假设某个Pod申请了2张GPU卡，那么GPU拓扑感知调度的目标是优先选出GPU1和GPU2的组合给该Pod，因为这个组合的通信带宽最大，Pod中应用程序在两张卡之间交换数据的时延最小。

![拓扑感知调度](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7486936061/p185646.png)

## Binpack和Spread算法

GPU共享调度中，在节点上支持按照Binpack和Spread算法为Pod分配GPU显存。

如下图所示，节点上有2张GPU卡，每张卡的显存为15 GiB，Pod1申请了2 GiB的显存，Pod2申请了3 GiB显存。

-   如果您设置的是Binpack算法，系统会优先将一张GPU卡的分配完后，再分配另一张GPU卡。如下图左边部分所示，两个Pod被分配到GPU1上，优先将GPU1上的显存分配完。
-   如果您设置的是Spread算法，系统会尽量将Pod申请的显存分散到各个GPU上。如下图中右边部分所示，Pod1和Pod2被分散到了GPU1和GPU2上。

![算法](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/7486936061/p185649.png)


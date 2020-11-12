# LocalVolume数据卷

有关阿里云ASK集群兼容社区LocalVolume本地挂载方案的详情，请参见[local](https://kubernetes.io/zh/docs/concepts/storage/volumes/#local)。

LocalVolume数据卷与HostPath数据卷的区别

|HostPath|LocalVolume|
|--------|-----------|
|不支持节点调度。|支持节点调度。|
|支持挂载目录、文件等格式。|支持挂载目录、裸设备。|
|支持自动创建目录。|不支持自动创建目录。|


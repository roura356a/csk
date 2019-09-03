# NAS卷使用说明 {#concept_l5p_qvs_vdb .concept}

您可以在容器服务 Kubernetes 集群中使用阿里云 NAS 数据卷。

目前阿里云 NAS 支持两种 CSI 挂载方式：

-   静态存储卷挂载
-   动态存储卷挂载

## 前提条件 {#section_sk3_m8k_hx6 .section}

使用 NAS 数据卷之前，您需要先在 NAS 管理控制台上创建文件系统，并在文件系统中添加挂载点。创建的 NAS 文件系统挂载点需要和您的集群位于同一 VPC。登录[文件存储管理控制台](https://nas.console.aliyun.com/)，可以创建一个 NAS 文件系统和挂载点。

例如，您的挂载点为`055f84ad83-ixxxx.cn-hangzhou.nas.aliyuncs.com `格式。

## 注意事项 {#section_nd3_f8g_0nf .section}

-   NAS 为共享存储，可以同时为多个 Pod 提供共享存储服务，即一个PVC可以同时被多个POD使用。
-   在没有卸载NAS文件系统前，务必不要删除NAS挂载点，否则会造成操作系统Hang。
-   NAS挂载点创建后，等待一定时间，待挂载点Ready后方可使用。
-   数据卷挂载协议推荐使用NAS v3。
-   使用NAS数据卷前，推荐升级Flexvolume到最新版本。

## 注意事项 {#section_shi_di9_uxv .section}

-   NAS 为共享存储，可以同时为多个 Pod 提供共享存储服务，即一个PVC可以同时被多个POD使用。
-   在没有卸载NAS文件系统前，务必不要删除NAS挂载点，否则会造成操作系统Hang。
-   NAS挂载点创建后，等待一定时间，待挂载点Ready后方可使用。
-   数据卷挂载协议推荐使用NAS v3。
-   使用NAS数据卷前，推荐升级CSI到最新版本。


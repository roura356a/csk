# OSS存储卷使用说明 {#concept_h11_5bt_vdb .concept}

您可以在阿里云容器服务 Kubernetes 集群中使用阿里云 OSS 数据卷。

目前，仅支持 OSS 静态存储卷，不支持 OSS 动态存储卷。

## 前提条件 {#section_kq6_ekp_2ze .section}

使用 OSS 静态存储卷之前，您需要先在 OSS 管理控制台上创建 Bucket。

## 使用说明 {#section_1zw_2oy_n6t .section}

-   OSS 为共享存储，可以同时为多个 Pod 提供共享存储服务。
-   bucket：目前只支持挂载 Bucket，不支持挂载 Bucket 下面的子目录或文件。
-   url：OSS endpoint，挂载 OSS 的接入域名，挂载节点和bucket相同region时，可使用内网地址。
-   akId：用户的 access id 值。
-   akSecret：用户的 access secret 值。
-   otherOpts：挂载 OSS 时支持定制化参数输入，格式为：`-o *** -o ***`。


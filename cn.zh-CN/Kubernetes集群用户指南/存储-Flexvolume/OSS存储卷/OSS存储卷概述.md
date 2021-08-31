# OSS存储卷概述

您可以在ACK集群中使用阿里云OSS数据卷。

目前，ACK仅支持OSS静态存储卷，不支持OSS动态存储卷。您可以通过使用创建PV和PVC的方式使用OSS静态存储卷。

## 前提条件

使用OSS静态存储卷之前，您需要先在OSS管理控制台上创建存储空间（Bucket）。具体操作，请参见[创建存储空间](/cn.zh-CN/快速入门/控制台快速入门/创建存储空间.md)。

## 使用说明

配置OSS静态存储卷的说明如下：

-   OSS为共享存储，可以同时为多个Pod提供共享存储服务。
-   bucket：目前只支持挂载存储空间Bucket，不支持挂载Bucket下面的子目录或文件。
-   url：OSS的Endpoint，挂载OSS的接入域名，挂载节点和存储空间的地域相同时，可使用内网地址。
-   akId：您的AccessKey ID值。
-   akSecret：您的AccessKey Secret值。
-   otherOpts：挂载OSS时支持定制化参数输入，格式为：`-o *** -o ***`。
-   您在使用OSS数据卷时，不要使用subpath的配置。
-   使用数据卷时，推荐每个应用使用独立的PV名字。
-   目前仅支持CentOS及Alibaba Cloud Linux 2操作系统。

## 注意事项

-   OSS数据卷是使用OSSFS文件进行挂载的FUSE文件系统，适合于读文件场景。例如：读配置文件、视频、图片文件等场景。
-   OSSFS不擅长于写文件的应用场景。如果您的业务是将文件写入存储的场景，推荐使用NAS存储卷服务。
-   内核态文件系统相较于用户态模拟的文件系统，在稳定性、性能上更具有优势，生产环境中推荐您使用NAS文件存储，而不推荐OSS对象存储。
-   OSSFS可以通过调整配置参数的方式，优化其在缓存、权限等方面的表现，请参见[常见问题](/cn.zh-CN/常用工具/ossfs/常见问题.md)、[ossfs/README-CN.md](https://github.com/aliyun/ossfs/blob/master/README-CN.md)、[FAQ](https://github.com/aliyun/ossfs/wiki/FAQ)。


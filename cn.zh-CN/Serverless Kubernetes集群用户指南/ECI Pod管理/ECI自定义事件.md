ECI自定义事件 {#main-1915324}
========================

本文将向您介绍ECI自定义事件及对应的解决方案。

事件信息 
-------------------------



|          事件名称           |  事件类型   |                               ++事件信息++                               |                                                                               解决方案                                                                               |
|-------------------------|---------|----------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ImageCacheNotFound      | Warning | The specified image cache %s does not exist.                         | 指定的镜像缓存不存在，您可以查询镜像缓存确保使用已存在的镜像缓存。                                                                                                                                |
| ImageCacheMissed        | Warning | Missed image cache.                                                  | 缺失镜像缓存，您需要先创建[镜像缓存](https://help.aliyun.com/document_detail/141281.html?spm=a2c4g.11186631.6.580.41cf4a09P4W93e)。                                |
| SuccessfulHitImageCache | Normal  | Successfully hit image cache %s.                                     | 成功匹配镜像缓存。                                                                                                                                                        |
| ImageCacheNotComplete   | Normal  | The production for specified image cache %s is not yet complete.     | 指定的镜像缓存还未达到ready状态，请确保镜像缓存ready后再进行使用。                                                                                                                           |
| Unauthorized            | Warning | No permission granted to eci service account for %s.                 | 没有正确地给服务账号授予默认角色，您需要确认是否[授权RAM角色](https://help.aliyun.com/document_detail/90794.html?spm=a2c4g.11174283.6.621.3e384b5bviIyNL)。                   |
| ResourceInsufficient    | Warning | %s not enough for %s.                                                | 当前Region和可用区的 ECI 已售罄，您可以配合[多可用区和多规格](https://help.aliyun.com/document_detail/157290.html?spm=a2c4g.11186623.6.574.363e6e85zxJbMr)创建ECI来提高创建成功率。 |
| QuotaFull               | Warning | %s quota is full.                                                    | 配额达到上限，您可以[提交工单](https://selfservice.console.aliyun.com/ticket/category/eci/recommend/3846)。                                                     |
| NoStock                 | Warning | Create ECI failed because of the specified instance is out of stock. | 当前Region和可用区的 ECI 已售罄，您可以配合[多可用区和多规格](https://help.aliyun.com/document_detail/157290.html?spm=a2c4g.11186623.6.574.363e6e85zxJbMr)创建ECI来提高创建成功率。 |
| SpotToBeReleased        | Warning | Spot ECI will be released in %s minutes                              | 您创建的抢占式实例即将过期，需要在业务上进行兼容处理。                                                                                                                                      |
| DiskCapacityQuotaFull   | Warning | Your disk capacity quota is exceeded.                                | 配额达到上限，您可以[提交工单](https://selfservice.console.aliyun.com/ticket/category/eci/recommend/3846)申请调整。                                                 |










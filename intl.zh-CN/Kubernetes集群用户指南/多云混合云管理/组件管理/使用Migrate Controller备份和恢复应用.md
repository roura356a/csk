---
keyword: [Velero, 备份应用, 恢复应用]
---

# 使用Migrate Controller备份和恢复应用

Migrate Controller是基于开源项目Velero开发的一个Kubernetes应用迁移的组件。本文介绍如何使用Migrate Controller组件在同个集群中备份和恢复应用。

[安装Migrate Controller](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/安装和使用Migrate Controller.md)

## 备份应用

使用Migrate Controller组件的Velero工具可以对Kubernetes集群下指定的一个或多个命名空间下的所有资源进行备份保存。备份命令如下：

```
velero backup create <BACKUP NAME> --include-namespaces <INCLUDE NAMESPACES>
```

以下举例说明如何使用命令备份一个命名空间和两个命名空间的所有资源：

-   备份命名空间nginx-examples下的应用：

    ```
    velero backup create backup-nginx --include-namespaces nginx-examples
    ```

-   备份命名空间nginx-examples和wordpress下的应用：

    ```
    velero backup create backup-nginx-and-wordpress --include-namespaces nginx-examples,wordpress
    ```


**说明：** 上面的应用备份操作并不会备份应用挂载的持久化数据卷数据，只备份Kubernetes资源的JSON格式编排文件。

如果您在备份应用的同时也希望可以备份应用挂载的持久化数据卷，请按以下步骤进行操作：

1.  为挂载了持久化数据卷的Pod添加注解backup.velero.io/backup-volumes。命令如下：

    ```
    kubectl -n <NAMESPACE> annotate pod/<POD NAME> backup.velero.io/backup-volumes=<POD VOLUME NAME>
    ```

2.  添加完注解后，继续创建备份。命令如下：

    ```
    velero backup create <BACKUP NAME> --include-namespaces <INCLUDE NAMESPACES>
    ```

    例如，备份命名空间nginx-examples下的Nginx应用及其持久化数据卷数据，持久化数据卷为nginx-logs并挂载在pod nginx-deployment-7477779c4f-rz95l上，则备份步骤为：

    1.  为Pod的数据卷添加注解。命令如下：

        ```
        kubectl -n nginx-example annotate pod/nginx-deployment-7477779c4f-rz95l backup.velero.io/backup-volumes=nginx-logs
        ```

    2.  创建备份。命令如下：

        ```
        velero backup create nginx-backup-with-pv --include-namespaces nginx-example
        ```


## 定时备份应用

如果您需要定时备份应用，则可以使用以下命令创建定时备份：

```
velero schedule create <NAME> --include-namespaces <NAMESPACE> --schedule "SCHEDULE"
```

以下举例说明如何使用命令进行每7天和每24小时的定时备份：

-   创建定时备份nginx-app-with-pv-schedule-01，每7天备份一次命名空间nginx-examples下的应用：

    ```
    velero schedule create nginx-app-with-pv-schedule-01 --include-namespaces nginx-examples --schedule "0 7 * * *"
    ```

-   创建定时备份nginx-app-with-pv-schedule-02，每24小时备份一次命名空间nginx-examples下的应用：

    ```
    velero schedule create nginx-app-with-pv-schedule-01 --include-namespaces nginx-examples --schedule "@every 24h"
    ```


## 恢复应用

本文以删除整个命名空间，然后再恢复应用为例，说明如何恢复应用。

1.  执行以下命令删除整个nginx-example命名空间。

    ```
    kubectl delete ns nginx-example
    ```

2.  执行以下命令创建恢复。

    ```
    velero restore create --from-backup nginx-backup-without-pv
    ```


**相关文档**  


[使用Migrate Controller跨集群迁移应用](/intl.zh-CN/Kubernetes集群用户指南/多云混合云管理/组件管理/使用Migrate Controller跨集群迁移应用.md)


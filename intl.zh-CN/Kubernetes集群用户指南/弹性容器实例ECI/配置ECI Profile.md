配置ECI Profile 
==================================

在Kubernetes场景中通过Virtual Kubelet使用ECI，如果遇到Pod无法无缝调度或者运行在ECI的问题，您可以尝试通过配置ECI Profile的方式来解决。本文介绍什么是ECI Profile以及如何配置ECI Profile。

ECI Profile介绍 
----------------------------------

在Kubernetes场景中通过Virtual Kubelet（简称VK）使用ECI时，可能会碰到Pod无法无缝调度到ECI，或者Pod调度到ECI后，为了生效ECI的功能特性（如镜像缓存）需要添加Pod Annotation。这类问题一般由集群管理员处理，但目前只能由研发人员通过修改Pod的YAML文件解决。

针对上述场景，ECI支持通过配置ECI Profile来解决。ECI Profile包括以下两个能力：

* ECI Scheduler

  在混合使用ECI和普通Node的场景下，一般可以通过以下几种方式将Pod调度到ECI：
  * [配置Pod标签](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)

    
  
  * [配置Namespace标签](/intl.zh-CN/Kubernetes集群用户指南/弹性容器实例ECI/通过部署ACK虚拟节点组件创建ECI Pod.md)

    
  
  * [配置ECI弹性调度](/intl.zh-CN/Kubernetes集群用户指南/调度/弹性调度/使用ECI弹性调度.md)

    
  

  

  但这些方式均需要对存量资源做一定的修改，无法做到零侵入。

  对于上述情况，ECI Scheduler基于Mutating Webhook机制实现了一种新的调度机制，即在ECI Profile中，您可以声明需要匹配的Namespace或者Pod的Label，对于Label能够匹配上的Pod，将被自动调度到ECI。
  

* ECI Effect

  对于ECI的一些功能特性，例如指定ECS实例规格，启用镜像缓存，设置NTP服务等，需要在Pod中追加Annotation或者Label来实现。更多信息，请参见[ECI Pod Annotation](t1860148.html#topic-1860148)。

  对于上述情况，ECI Effect实现了自动追加Annotation和Label的功能，即在ECI Profile中，您可以声明需要匹配的Namespace或者Pod的Label，以及需要追加的Annotation和Label，对于Label能够匹配上的Pod，将自动追加配置的Annotation和Label。
  




配置说明 
-------------------------

ECI Profile会读取kube-system命名空间下的eci-profile配置文件，然后按照配置文件中的selectors去匹配Pod，对于Label能够匹配上的Pod，将自动调度到ECI，或者追加Annotation和Label，以便生效ECI的功能特性。

您可以通过以下两种方式编辑eci-profile配置文件：

* 通过kubectl edit命令

  ```shell
  kubectl edit configmap eci-profile -n kube-system
  ```

  

* 通过容器服务管理控制台

  1. 在[容器服务管理控制台](https://cs.console.aliyun.com/)的集群页面，找到要配置的集群，单击集群名称进入集群详情页面。

     
  
  2. 在左侧导航栏选择 **配置管理\>配置项** 。

     
  
  3. 选择命名空间为 **kube-system** 。

     
  
  4. 找到eci-profile进行编辑。

     
  

  




一个典型的eci-profile配置文件示例如下：

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: eci-profile
  namespace: kube-system
data:
  vpcId: "vpc-xxx"
  securityGroupId: "sg-xxx"
  vswitchIds: "vsw-111,vsw-222"
  enableClusterIp: "true"
  enableHybridMode: "false"
  enablePrivateZone: "false"
  selectors: |
    [
        {
            "name":"default-selector-1",
            "objectSelector":{
                "matchLabels":{
                    "alibabacloud.com/eci":"true"
                }
            }
        },
        {
            "name":"default-selector-2",
            "objectSelector":{
                "matchLabels":{
                    "eci":"true"
                }
            }
        },
        {
            "name":"default-selector-3",
            "namespaceSelector":{
                "matchLabels":{
                    "alibabacloud.com/eci":"true"
                }
            }
        },
        {
            "name":"default-selector-4",
            "namespaceSelector":{
                "matchLabels":{
                    "eci":"true"
                }
            }
        },
        {
            "name":"default-selector-5",
            "namespaceSelector":{  
                "matchLabels":{
                    "virtual-node-affinity-injection":"enabled"
                }
            },
            "effect":{  #需要动态追加的Annotation和Label
            "annotations":{
                "k8s.aliyun.com/eci-image-cache": "true"
            },
            "labels":{
                "created-by-eci":"true"
            }
          }
        }
    ]
```



在上述配置文件中，selectors包含了ECI Scheduler和ECI Effect的配置。在每个selector中，您必须声明selector的名称，可以根据需要声明以下信息：

* namespaceSelector：要匹配的Namespace Label。

  

* objectSelector：要匹配的Pod Label。

  

* effect：需要动态追加的Annotation和Label。

  



**说明**

eci-profile除了支持配置ECI Scheduler和ECI Effect外，还支持配置安全组、交换机、资源组等信息实现动态更新（无需重启VK）。本文主要说明如何配置ECI Scheduler和ECI Effect，即上述配置文件中的selectors。

配置ECI Scheduler示例 
--------------------------------------

在eci-profile的selectors中，您可以以数组的形式声明需要匹配的Namespace或者Pod的Label。对于Label能够匹配上的Pod，将会被自动调度到ECI。

配置示例如下：

```yaml
  selectors: |
   [
      {
        "name":"demo",  #必填，不能为空
        "namespaceSelector":{  #选填，K8s的Namespace Label（namespaceSelector和objectSelector至少配置一个）
            "matchLabels":{  #需要匹配的Label，如果填写多个，为与关系
                "department":"bigdata"
            }
        },
        "objectSelector":{  #选填，K8s的Pod Label（namespaceSelector和objectSelector至少配置一个）
            "matchLabels":{  #需要匹配的Label，如果填写多个，为与关系
                "type":"offline-task"
              }
          }
      }
   ]
```


**注意**

在一个selector中，您至少要配置namespaceSelector和objectSelector中的一个，如果两者同时配置了，则Pod需要同时匹配两者。

上述selectors中，声明了一个名为demo的selector，可以实现以下功能：

如果Pod所属命名空间含有department=bigdata标签，并且Pod本身含有type=offline-task标签，则该Pod将自动被调度到ECI。

配置ECI Effect示例 
-----------------------------------

在eci-profile的selectors中，您可以以数组的形式声明需要匹配的Namespace或者Pod的Label，以及需要动态追加的Annotation和Label。对于Label能够匹配上的Pod，将自动追加配置的Annotation和Label。

配置示例如下：

```yaml
  selectors: |
   [
    {
        "name":"demo",  #必填，不能为空
        "namespaceSelector":{  #选填，K8s的Namespace Label
            "matchLabels":{  #需要匹配的Label，如果填写多个，为与关系
                "department":"bigdata"
            }
        },
        "objectSelector":{  #选填，K8s的Pod Label
            "matchLabels":{  #需要匹配的Label，如果填写多个，为与关系
                "type":"offline-task"
            }
        },
        "effect":{  #需要动态追加的Annotation和Label
            "annotations":{
                "k8s.aliyun.com/eci-image-cache": "true"
            },
            "labels":{
                "created-by-eci":"true"
            }
        }
    }
   ]
```


**注意**



* 在一个selector中，您可以根据需要选择配置namespaceSelector和objectSelector。如果两者均未配置，则effect对所有调度到ECI的Pod均生效。

  

* 如果配置了多个selector，将按照顺序匹配selector。匹配成功后，将自动把effect中声明的Annotation和Label追加到Pod中（只追加，不覆盖）。对于重复的Annotation和Label，Pod原有声明的值优先级最高，其次是顺序匹配上的第一个selector中的effect，以此类推。

  




上述selectors中，声明了一个名为demo的selector，可以实现以下功能：

如果Pod所在的命名空间有department=bigdata标签，并且Pod本身含有type=offline-task标签，则该Pod将自动调度到ECI，并且自动开启镜像缓存功能，增加created-by-eci=true标签。

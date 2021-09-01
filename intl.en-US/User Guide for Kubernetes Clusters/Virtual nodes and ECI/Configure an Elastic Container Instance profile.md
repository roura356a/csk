Configure an Elastic Container Instance profile 
====================================================================

When you use elastic container instances based on Virtual Kubelet in Kubernetes scenarios, pods may fail to be seamlessly scheduled to or fail to run on elastic container instances. In this case, you can configure Elastic Container Instance profiles to resolve these issues. This topic describes what an Elastic Container Instance profile is and how to configure an Elastic Container Instance profile. 

Introduction to Elastic Container Instance profile 
-----------------------------------------------------------------------

When you use elastic container instances based on Virtual Kubelet in Kubernetes scenarios, pods may fail to be seamlessly scheduled to elastic container instances. Alternatively, after pods are scheduled to elastic container instances, you may need to add pod annotations for Elastic Container Instance features such as image cache to take effect. In most cases, this type of issue is handled by the cluster administrator. However, the current issue can be resolved only by asking developers to modify the YAML file of the pod. 

You can configure Elastic Container Instance profiles to resolve the preceding issues. An Elastic Container Instance profile provides the following features:

* ECI Scheduler

  When you use elastic container instances together with regular nodes, you can use one of the following methods to schedule pods to elastic container instances:
  * [Add a label to the pod](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

    
  
  * [Add a label to the namespace to which the pod belongs](/intl.en-US/User Guide for Kubernetes Clusters/Virtual nodes and ECI/Deploy the virtual node controller and use it to create Elastic Container Instance-based pods.md).

    
  
  * [Configure Elastic Container Instance elastic scheduling](/intl.en-US/User Guide for Kubernetes Clusters/Scheduling/Node scheduling/Use ECI elastic scheduling.md).

    
  

  

  These methods are not intrusion-free and require modifications to existing resources. 

  Elastic Container Instance Scheduler implements a new scheduling mechanism based on the Mutating Webhook mechanism. You can declare the namespace labels or pod labels to be matched in Elastic Container Instance profiles. Pods that have the declared labels are automatically scheduled to elastic container instances.
  

* ECI Effect

  You must add annotations and labels to pods for specific Elastic Container Instance features to take effect. These features include specifying Elastic Compute Service (ECS) instance types, enabling image cache, and configuring the Network Time Protocol (NTP) service. For more information, see [Pod annotations supported by Elastic Container Instance](t1860148.html#topic-1860148). 

  Elastic Container Instance Effect allows you to automatically add annotations and labels. In Elastic Container Instance profiles, you can declare the namespace labels or pod labels to be matched and specify the annotations and labels to be added. If pods have the declared labels, the specified annotations and labels are automatically added to the pods.
  




Configurations 
-----------------------------------

Elastic Container Instance profiles read the eci-profile configuration file in the kube-system namespace and match pods based on the selectors specified in the configuration file. If pods have the labels contained in the selectors, the pods are automatically scheduled to elastic container instances, or annotations and labels are automatically added to the pods for Elastic Container Instance features to take effect. 

You can use one of the following methods to edit the eci-profile configuration file:

* Run the kubectl edit command through a command-line interface (CLI)

  ```shell
  kubectl edit configmap eci-profile -n kube-system
  ```

  

* Use the Container Service for Kubernetes (ACK) console

  1. On the Clusters page of the [ACK console](https://cs.console.aliyun.com/), find the cluster that you want to manage and click its name to go to the cluster details page.

     
  
  2. In the left-side navigation pane, choose **Configurations \> ConfigMaps** .

     
  
  3. Select the **kube-system** namespace.

     
  
  4. Find and edit the eci-profile configuration file.

     
  

  




Example of the eci-profile configuration file:

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
            "effect":{  #The annotations and labels to be dynamically added.
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



In the preceding example, the selectors section contains the configurations of Elastic Container Instance Scheduler and Elastic Container Instance Effect. For each selector, you must declare the name of the selector. You can declare the following fields based on your needs:

* namespaceSelector: the namespace labels to be matched.

  

* objectSelector: the pod labels to be matched.

  

* effect: the annotations and labels to be dynamically added.

  



**Note**

You can configure Elastic Container Instance Scheduler and Elastic Container Instance Effect in the eci-profile configuration file. In addition, you can also configure to dynamically update other settings such as security groups, vSwitches, and resource groups without the need to restart Virtual Kubelet. This topic focuses on how to configure Elastic Container Instance Scheduler and Elastic Container Instance Effect by using selectors in the eci-profile configuration file.

Example on how to configure Elastic Container Instance Scheduler 
-------------------------------------------------------------------------------------

In the selectors section of the eci-profile configuration file, you can declare the namespace labels or pod labels to be matched in the form of arrays. The pods that have matching labels are automatically scheduled to elastic container instances. 

The following sample code provides an example on how to configure Elastic Container Instance Scheduler:

```yaml
  selectors: |
   [
      {
        "name":"demo",  #Required. The name of the selector.
        "namespaceSelector":{  #Optional. The namespace labels to be matched. You must specify at least either namespaceSelector or objectSelector.
            "matchLabels":{  #The labels to be matched. If you specify multiple labels, these labels have logical AND relations.
                "department":"bigdata"
            }
        },
        "objectSelector":{  #Optional. The pod labels to be matched. You must specify at least either namespaceSelector or objectSelector.
            "matchLabels":{  #The labels to be matched. If you specify multiple labels, these labels have logical AND relations.
                "type":"offline-task"
              }
          }
      }
   ]
```


**Notice**

In a selector, you must configure at least either namespaceSelector or objectSelector. If you configure both namespaceSelector and objectSelector, only pods that have all the labels specified in namespaceSelector and objectSelector are automatically scheduled to elastic container instances.

In the preceding selectors, a selector named demo is declared to implement the following feature:

If the namespace of a pod has the department=bigdata label and the pod has the type=offline-task label, the pod is automatically scheduled to an elastic container instance.

Example on how to configure Elastic Container Instance Effect 
----------------------------------------------------------------------------------

In the selectors section of the eci-profile configuration file, you can declare the namespace labels or pod labels to be matched in the form of arrays and specify the annotations and labels to be dynamically added. If pods have the declared labels, the specified annotations and labels are automatically added to the pods. 

The following sample code provides an example on how to configure Elastic Container Instance Effect:

```yaml
  selectors: |
   [
    {
        "name":"demo",  #Required. The name of the selector.
        "namespaceSelector":{  #Optional. The namespace labels to be matched.
            "matchLabels":{  #The labels to be matched. If you specify multiple labels, these labels have logical AND relations.
                "department":"bigdata"
            }
        },
        "objectSelector":{  #Optional. The pod labels to be matched.
            "matchLabels":{  #The labels to be matched. If you specify multiple labels, these labels have logical AND relations.
                "type":"offline-task"
            }
        },
        "effect":{  #The annotations and labels to be dynamically added.
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


**Notice**



* In a selector, you can configure namespaceSelector and objectSelector based on your requirements. If neither namespaceSelector or objectSelector is specified, the effect settings take effect on all pods that are scheduled to elastic container instances.

  

* If you configure multiple selectors, these selectors are matched in sequence. After matching pods are found, the annotations and labels specified in the effect settings are automatically added to the pods. These annotations and labels do not overwrite existing annotations and labels of the pods. If duplicate annotations or labels exist, the annotations or labels with higher priorities are used. The existing annotations and labels of the pods have a higher priority than the annotations and labels specified in the effect settings of matched selectors. The priorities of annotations or labels in the effect settings of the selectors descend in the order in which the selectors are matched.

  




In the preceding selectors, a selector named demo is declared to implement the following feature:

If the namespace of a pod has the department=bigdata label and the pod has the type=offline-task label, the pod is automatically scheduled to an elastic container instance. At the same time, the image cache feature is enabled, and the created-by-eci=true label is added to the pod.

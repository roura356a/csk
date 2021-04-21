# 





## 





## 

```
POST /clusters/ClusterId/nodepools HTTP/1.1 
Content-Type:application/json
{
  "auto_scaling" : {
    "enable" : Boolean,
    "max_instances" : Long,
    "min_instances" : Long,
    "type" : "String",
    "is_bond_eip" : Boolean,
    "eip_internet_charge_type" : "String",
    "eip_bandwidth" : Long
  },
  "kubernetes_config" : {
    "cms_enabled" : Boolean,
    "cpu_policy" : "String",
    "labels" : [ {
      "key" : "String",
      "value" : "String"
    } ],
    "runtime" : "String",
    "runtime_version" : "String",
    "taints" : [ {
      "key" : "String",
      "value" : "String",
      "effect" : "String"
    } ],
    "user_data" : "String"
  },
  "nodepool_info" : {
    "name" : "String",
    "resource_group_id" : "String"
  },
  "scaling_group" : {
    "auto_renew" : Boolean,
    "auto_renew_period" : Long,
    "data_disks" : [ {
      "category" : "String",
      "size" : Long,
      "encrypted" : "String",
      "auto_snapshot_policy_id" : "String"
    } ],
    "image_id" : "String",
    "instance_charge_type" : "String",
    "instance_types" : [ "String" ],
    "key_pair" : "String",
    "login_password" : "String",
    "period" : Long,
    "period_unit" : "String",
    "platform" : "String",
    "rds_instances" : [ "String" ],
    "spot_strategy" : "String",
    "spot_price_limit" : [ {
      "instance_type" : "String",
      "price_limit" : "String"
    } ],
    "scaling_policy" : "String",
    "security_group_id" : "String",
    "system_disk_category" : "String",
    "system_disk_size" : Long,
    "tags" : [ {
      "key" : "String",
      "value" : "String"
    } ],
    "vswitch_ids" : [ "String" ],
    "multi_az_policy" : "String",
    "on_demand_base_capacity" : Long,
    "on_demand_percentage_above_base_capacity" : Long,
    "spot_instance_pools" : Long,
    "spot_instance_remedy" : Boolean,
    "compensate_with_on_demand" : Boolean
  },
  "tee_config" : {
    "tee_enable" : Boolean
  },
  "management" : {
    "enable" : Boolean,
    "auto_repair" : Boolean,
    "upgrade_config" : {
      "auto_upgrade" : Boolean,
      "surge" : Long,
      "surge_percentage" : Long,
      "max_unavailable" : Long
    }
  },
  "count" : Long
}
```



## 

||||||
|--|--|--|--|--|
|ClusterId|String||c61da77e8bfbc4c4c999af2b51b65\*\*\*\*| |

||||||
|--|--|--|--|--|
|auto\_scaling|Object|| | |
|enable|Boolean||true|

 -   -   
  |
|max\_instances|Long||10| |
|min\_instances|Long||1| |
|type|String||cpu|

 -   -   -   -   
  |
|is\_bond\_eip|Boolean||true|

 -   -   
  |
|eip\_internet\_charge\_type|String||PayByBandwidth|

 -   -   
  |
|eip\_bandwidth|Long||5| |
|kubernetes\_config|Object|| | |
|cms\_enabled|Boolean||true|

 -   -   
  |
|cpu\_policy|String||none|

 -   -   
  |
|labels|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|| |

  |
|runtime|String||docker| |
|runtime\_version|String||19.03.5| |
|taints|Array of [taint](/intl.en-US/API Reference/Commonly used parameters.md)|| | |
|user\_data|String||dGhpcyBpcyBhIGV4YW1wbGU=| |
|nodepool\_info|Object|| | |
|name|String||cluster-demo| |
|resource\_group\_id|String||rg-acfmyvw3wjmb\*\*\*\*| |
|scaling\_group|Object|| | |
|auto\_renew|Boolean||true|

 -   -    |
|auto\_renew\_period|Long||1|

  |
|data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)|| | |
|image\_id|String||aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd| |
|instance\_charge\_type|String||PrePaid|

 -   -   
  |
|instance\_types|Array of String||ecs.d1ne.2xlarge| |
|key\_pair|String||np-key-name|

 **Note:** |
|login\_password|String||Hello1234| |
|period|Long||1|

`PrePaid`

 Period |
|period\_unit|String||Month| |
|platform|String||Linux|

 -   `CentOS`
-   `AliyunLinux`
-   `Windows`
-   `WindowsCore`

  |
|rds\_instances|Array of String||rds-\*\*\*\*| |
|spot\_strategy|String||NoSpot|

 -   -   -   
  |
|spot\_price\_limit|Array|| | |
|instance\_type|String||ecs.c6.large| |
|price\_limit|String||0.39| |
|scaling\_policy|String||release|

 -   -   
  |
|security\_group\_id|String||sg-wz9a8g2mt6x5llu0\*\*\*\*| |
|system\_disk\_category|String||cloud\_efficiency|

 -   -   -   
  |
|system\_disk\_size|Long||120|

  |
|tags|Array|| | |
|key|String||node-k-1| |
|value|String||node-v-1| |
|vswitch\_ids|Array of String||vsw-wz9mfnhmssud6eicu\*\*\*\*| |
|multi\_az\_policy|String||COST\_OPTIMIZED|

 -   -   **Note:**

-   
  |
|on\_demand\_base\_capacity|Long||0| |
|on\_demand\_percentage\_above\_base\_capacity|Long||20| |
|spot\_instance\_pools|Long||5| |
|spot\_instance\_remedy|Boolean||false|

 -   -    |
|compensate\_with\_on\_demand|Boolean||true|

 -   -    |
|tee\_config|Object|| | |
|tee\_enable|Boolean||true| |
|management|Object|| | |
|enable|Boolean||false|

 -   -    |
|auto\_repair|Boolean||false|

 -   -    |
|upgrade\_config|Object|| | |
|auto\_upgrade|Boolean||false|

 -   -    |
|surge|Long||0| |
|surge\_percentage|Long||0| |
|max\_unavailable|Long||0|

 

  |
|count|Long||1| |



## 

```
HTTP/1.1 200
Content-Type:application/json
{
  "nodepool_id" : "String"
}
```



## 

||Type|||
|--|----|--|--|
|nodepool\_id|String|np31da1b38983f4511b490fc62108a\*\*\*\*| |



## 



```
POST /clusters/c61da77e8bfbc4c4c999af2b51b65****/nodepools HTTP/1.1 
Content-Type:application/json
{
  "auto_scaling" : {
    "enable" : true,
    "max_instances" : 10,
    "min_instances" : 1,
    "type" : "cpu",
    "is_bond_eip" : true,
    "eip_internet_charge_type" : "PayByBandwidth",
    "eip_bandwidth" : 5
  },
  "kubernetes_config" : {
    "cms_enabled" : true,
    "cpu_policy" : "none",
    "labels" : [ {
      "key" : "env",
      "value" : "prod"
    } ],
    "runtime" : "docker",
    "runtime_version" : "19.03.5",
    "taints" : [ {
      "key" : "key",
      "value" : "value",
      "effect" : "NoSchedule"
    } ],
    "user_data" : "dGhpcyBpcyBhIGV4YW1wbGU="
  },
  "nodepool_info" : {
    "name" : "cluster-demo",
    "resource_group_id" : "rg-acfmyvw3wjmb****"
  },
  "scaling_group" : {
    "auto_renew" : true,
    "auto_renew_period" : 1,
    "data_disks" : [ {
      "category" : "cloud_ssd",
      "size" : 40,
      "encrypted" : "true",
      "auto_snapshot_policy_id" : "sp-2zej1nogjvovnz4z****"
    } ],
    "image_id" : "aliyun_2_1903_x64_20G_alibase_20200529.vhd",
    "instance_charge_type" : "PrePaid",
    "instance_types" : [ "ecs.d1ne.2xlarge" ],
    "key_pair" : "np-key-name",
    "login_password" : "Hello1234",
    "period" : 1,
    "period_unit" : "Month",
    "platform" : "Linux",
    "rds_instances" : [ "rds-***" ],
    "spot_strategy" : "NoSpot",
    "spot_price_limit" : [ {
      "instance_type" : "ecs.c6.large",
      "price_limit" : "0.39"
    } ],
    "scaling_policy" : "release",
    "security_group_id" : "sg-wz9a8g2mt6x5llu0****",
    "system_disk_category" : "cloud_efficiency",
    "system_disk_size" : 120,
    "tags" : [ {
      "key" : "node-k-1",
      "value" : "node-v-1"
    } ],
    "vswitch_ids" : [ "vsw-wz9mfnhmssud6eicu****" ],
    "multi_az_policy" : "COST_OPTIMIZED",
    "on_demand_base_capacity" : 0,
    "on_demand_percentage_above_base_capacity" : 20,
    "spot_instance_pools" : 5,
    "spot_instance_remedy" : false,
    "compensate_with_on_demand" : true
  },
  "tee_config" : {
    "tee_enable" : true
  },
  "management" : {
    "enable" : false,
    "auto_repair" : false,
    "upgrade_config" : {
      "auto_upgrade" : false,
      "surge" : 0,
      "surge_percentage" : 0,
      "max_unavailable" : 0
    }
  },
  "count" : 1
}
```





```
HTTP/1.1 200 OK
Content-Type:application/xml

<nodepool_id>np31da1b38983f4511b490fc62108a****</nodepool_id>
```



```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "nodepool_id" : "np31da1b38983f4511b490fc62108a****"
}
```



## 




# 





## 





## 

```
PUT /clusters/ClusterId/nodepools/NodepoolId HTTP/1.1 
Content-Type:application/json
{
  "auto_scaling" : {
    "eip_bandwidth" : Long,
    "eip_internet_charge_type" : "String",
    "enable" : Boolean,
    "is_bond_eip" : Boolean,
    "max_instances" : Long,
    "min_instances" : Long,
    "type" : "String"
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
    "data_disks" : [ {
      "category" : "String",
      "size" : Long,
      "encrypted" : "String",
      "auto_snapshot_policy_id" : "String"
    } ],
    "instance_charge_type" : "String",
    "period" : Long,
    "period_unit" : "String",
    "auto_renew" : Boolean,
    "auto_renew_period" : Long,
    "platform" : "String",
    "image_id" : "String",
    "spot_strategy" : "String",
    "spot_price_limit" : [ {
      "instance_type" : "String",
      "price_limit" : "String"
    } ],
    "instance_types" : [ "String" ],
    "key_pair" : "String",
    "login_password" : "String",
    "rds_instances" : [ "String" ],
    "scaling_policy" : "String",
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
  "update_nodes" : Boolean
}
```



## 

||||||
|--|--|--|--|--|
|ClusterId|String||c23421cfa74454bc8b37163fd19af\*\*\*\*| |
|NodepoolId|String||p31da1b38983f4511b490fc62108a\*\*\*\*| |

||||||
|--|--|--|--|--|
|auto\_scaling|Object|| | |
|eip\_bandwidth|Long||5| |
|eip\_internet\_charge\_type|String||PayByBandwidth|

 -   -   
  |
|enable|Boolean||true|

 -   -   
  |
|is\_bond\_eip|Boolean||true|

 -   -   
  |
|max\_instances|Long||10| |
|min\_instances|Long||2| |
|type|String||cpu|

 -   -   -   -   
  |
|kubernetes\_config|Object|| | |
|cms\_enabled|Boolean||true|

 -   -   
  |
|cpu\_policy|String||none|

 -   -   
  |
|labels|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|| | |
|runtime|String||docker| |
|runtime\_version|String||19.03.5| |
|taints|Array of [taint](/intl.en-US/API Reference/Commonly used parameters.md)|| | |
|user\_data|String||IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFDSyEi| |
|nodepool\_info|Object|| | |
|name|String||default-nodepool|

  |
|resource\_group\_id|String||rg-acfmyvw3wjm\*\*\*\*| |
|scaling\_group|Object|| | |
|data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)|| | |
|instance\_charge\_type|String||PostPaid|

 -   -   
  |
|period|Long||1|

  |
|period\_unit|String||Month|

 

  |
|auto\_renew|Boolean||true|

 -   -   
  |
|auto\_renew\_period|Long||1|

  |
|platform|String||AliyunLinux|

 -   `AliyunLinux`
-   `CentOS`
-   `Windows`
-   `WindowsCore` |
|image\_id|String||aliyun\_2\_1903\_x64\_20G\_alibase\_20200904.vhd| |
|spot\_strategy|String||SpotWithPriceLimit|

 -   -   -   
  |
|spot\_price\_limit|Array|| | |
|instance\_type|String||ecs.c6.large| |
|price\_limit|String||0.39| |
|instance\_types|Array of String||ecs.c6.large| |
|key\_pair|String||pro-nodepool| |
|login\_password|String||Hello1234| |
|rds\_instances|Array of String||rds-xxx| |
|scaling\_policy|String||release|

 -   -    |
|system\_disk\_category|String||cloud\_efficiency|

 -   -   
  |
|system\_disk\_size|Long||120|

 

 

  |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)|| | |
|vswitch\_ids|Array of String||vsw-wz9uwxhawmtzg7u9h\*\*\*\*| |
|multi\_az\_policy|String||BALANCE|

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
|tee\_enable|Boolean||false|

 -   -   
  |
|management|Object|| | |
|enable|Boolean||true|

 -   -   
  |
|auto\_repair|Boolean||true|

 -   -   
  |
|upgrade\_config|Object|| | |
|auto\_upgrade|Boolean||true|

 -   -   
  |
|surge|Long||5|

 **Note:** |
|surge\_percentage|Long||0| |
|max\_unavailable|Long||1|

 

  |
|update\_nodes|Boolean||true| |



## 

```
HTTP/1.1 200
Content-Type:application/json
{
  "task_id" : "String",
  "nodepool_id" : "String"
}
```



## 

|||||
|--|--|--|--|
|task\_id|String|T-5fd211e924e1d00787000293| |
|nodepool\_id|String|np737c3ac1ac684703b9e10673aa2c\*\*\*\*| |



## 



```
PUT /clusters/c23421cfa74454bc8b37163fd19af****/nodepools/p31da1b38983f4511b490fc62108a**** HTTP/1.1 
Content-Type:application/json
{
  "auto_scaling" : {
    "eip_bandwidth" : 5,
    "eip_internet_charge_type" : "PayByBandwidth",
    "enable" : true,
    "is_bond_eip" : true,
    "max_instances" : 10,
    "min_instances" : 2,
    "type" : "cpu"
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
    "user_data" : "IyEvdXNyL2Jpbi9iYXNoCmVjaG8gIkhlbGxvIEFDSyEi"
  },
  "nodepool_info" : {
    "name" : "default-nodepool",
    "resource_group_id" : "rg-acfmyvw3wjm****"
  },
  "scaling_group" : {
    "data_disks" : [ {
      "category" : "cloud_ssd",
      "size" : 40,
      "encrypted" : "true",
      "auto_snapshot_policy_id" : "sp-2zej1nogjvovnz4z****"
    } ],
    "instance_charge_type" : "PostPaid",
    "period" : 1,
    "period_unit" : "Month",
    "auto_renew" : true,
    "auto_renew_period" : 1,
    "platform" : "AliyunLinux",
    "image_id" : "aliyun_2_1903_x64_20G_alibase_20200904.vhd",
    "spot_strategy" : "SpotWithPriceLimit",
    "spot_price_limit" : [ {
      "instance_type" : "ecs.c6.large",
      "price_limit" : "0.39"
    } ],
    "instance_types" : [ "ecs.c6.large" ],
    "key_pair" : "pro-nodepool",
    "login_password" : "Hello1234",
    "rds_instances" : [ "rds-xxx" ],
    "scaling_policy" : "release",
    "system_disk_category" : "cloud_efficiency",
    "system_disk_size" : 120,
    "tags" : [ {
      "key" : "env",
      "value" : "prod"
    } ],
    "vswitch_ids" : [ "vsw-wz9uwxhawmtzg7u9h****" ],
    "multi_az_policy" : "BALANCE",
    "on_demand_base_capacity" : 0,
    "on_demand_percentage_above_base_capacity" : 20,
    "spot_instance_pools" : 5,
    "spot_instance_remedy" : false,
    "compensate_with_on_demand" : true
  },
  "tee_config" : {
    "tee_enable" : false
  },
  "management" : {
    "enable" : true,
    "auto_repair" : true,
    "upgrade_config" : {
      "auto_upgrade" : true,
      "surge" : 5,
      "surge_percentage" : 0,
      "max_unavailable" : 1
    }
  },
  "update_nodes" : true
}
```





```
HTTP/1.1 200 OK
Content-Type:application/xml

<task_id>T-5fd211e924e1d00787000293</task_id>
<nodepool_id>np737c3ac1ac684703b9e10673aa2c****</nodepool_id>
```



```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "task_id" : "T-5fd211e924e1d00787000293",
  "nodepool_id" : "np737c3ac1ac684703b9e10673aa2c****"
}
```



## 




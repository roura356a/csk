# 





## 





## 

```
GET /clusters/ClusterId/nodepools HTTP/1.1 
Content-Type:application/json
```



## 

||||||
|--|--|--|--|--|
|ClusterId|String||cc0f87de0b8fb403f86e10e204f83\*\*\*\*| |



## 

```
HTTP/1.1 200
Content-Type:application/json
{
  "nodepools" : [ {
    "auto_scaling" : {
      "eip_bandwidth" : Long,
      "is_bond_eip" : Boolean,
      "eip_internet_charge_type" : "String",
      "enable" : Boolean,
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
      "created" : "String",
      "is_default" : Boolean,
      "name" : "String",
      "nodepool_id" : "String",
      "region_id" : "String",
      "resource_group_id" : "String",
      "type" : "String",
      "updated" : "String"
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
      "multi_az_policy" : "String",
      "on_demand_base_capacity" : Long,
      "on_demand_percentage_above_base_capacity" : Long,
      "spot_instance_pools" : Long,
      "spot_instance_remedy" : Boolean,
      "compensate_with_on_demand" : Boolean,
      "period" : Long,
      "period_unit" : "String",
      "platform" : "String",
      "ram_policy" : "String",
      "spot_strategy" : "String",
      "spot_price_limit" : [ {
        "instance_type" : "String",
        "price_limit" : "String"
      } ],
      "rds_instances" : [ "String" ],
      "scaling_group_id" : "String",
      "scaling_policy" : "String",
      "security_group_id" : "String",
      "system_disk_category" : "String",
      "system_disk_size" : Long,
      "tags" : [ {
        "key" : "String",
        "value" : "String"
      } ],
      "vswitch_ids" : [ "String" ],
      "login_password" : "String",
      "key_pair" : "String"
    },
    "status" : {
      "failed_nodes" : Long,
      "healthy_nodes" : Long,
      "initial_nodes" : Long,
      "offline_nodes" : Long,
      "removing_nodes" : Long,
      "serving_nodes" : Long,
      "state" : "String",
      "total_nodes" : Long
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
    }
  } ]
}
```



## 

|||||
|--|--|--|--|
|nodepools|Array| | |
|auto\_scaling|object| | |
|eip\_bandwidth|Long|5| |
|is\_bond\_eip|Boolean|true|

 -   -    |
|eip\_internet\_charge\_type|String|PayByBandwidth|

 -   -    |
|enable|Boolean|true|

 -   -    |
|max\_instances|Long|10| |
|min\_instances|Long|2| |
|type|String|cpu|

 -   -   -   -    |
|kubernetes\_config|object| | |
|cms\_enabled|Boolean|true|

 -   -    |
|cpu\_policy|String|none|

 -   -    |
|labels|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)| | |
|runtime|String|docker| |
|runtime\_version|String|19.03.5| |
|taints|Array of [taint](/intl.en-US/API Reference/Commonly used parameters.md)| | |
|user\_data|String|IyEvYmluL3NoCmVjaG8gIkhlbGxvIEFD\*\*\*\*| |
|nodepool\_info|object| | |
|created|String|2020-09-27T19:14:09.156823496+08:00| |
|is\_default|Boolean|true|

 -   -    |
|name|String|default-nodepool|

  |
|nodepool\_id|String|np615c0e0966124216a0412e10afe0\*\*\*\*| |
|region\_id|String|cn-beijing| |
|resource\_group\_id|String|rg-acfmyvw3wjm\*\*\*\*| |
|type|String|ess| |
|updated|String|2020-09-27T20:37:46+08:00| |
|scaling\_group|object| | |
|auto\_renew|Boolean|false|

 -   -    |
|auto\_renew\_period|Long|0|

  |
|data\_disks|Array of [data\_disk](/intl.en-US/API Reference/Commonly used parameters.md)| | |
|image\_id|String|aliyun\_2\_1903\_x64\_20G\_alibase\_20200529.vhd| |
|instance\_charge\_type|String|PostPaid|

 -   -    |
|instance\_types|Array of String|ecs.n4.large| |
|multi\_az\_policy|String|COST\_OPTIMIZED|

 -   -   **Note:**

-    |
|on\_demand\_base\_capacity|Long|0| |
|on\_demand\_percentage\_above\_base\_capacity|Long|20| |
|spot\_instance\_pools|Long|5| |
|spot\_instance\_remedy|Boolean|false|

 -   -    |
|compensate\_with\_on\_demand|Boolean|true|

 -   -    |
|period|Long|0|

  |
|period\_unit|String|Month|

  |
|platform|String|AliyunLinux|

 -   `CentOS`
-   `AliyunLinux`
-   `Windows`
-   `WindowsCore` |
|ram\_policy|String|KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e\*\*\*\*| |
|spot\_strategy|String|NoSpot|

 -   -   -   
  |
|spot\_price\_limit|Array| | |
|instance\_type|String|ecs.c6.large| |
|price\_limit|String|0.39| |
|rds\_instances|Array of String|rm-xxx| |
|scaling\_group\_id|String|asg-2ze8n5qw4atggut8\*\*\*\*| |
|scaling\_policy|String|release|

 -   -    |
|security\_group\_id|String|sg-2ze1iuk12m2sb4c4\*\*\*\*| |
|system\_disk\_category|String|cloud\_efficiency|

 -   -    |
|system\_disk\_size|Long|120|

  |
|tags|Array of [tag](/intl.en-US/API Reference/Commonly used parameters.md)| |

 -   -    |
|vswitch\_ids|Array of String|vsw-2ze3ds0mdip0hdz8i\*\*\*\*| |
|login\_password|String|\*\*\*\*\*\*|

  |
|key\_pair|String|pro-nodepool|

  |
|status|object| | |
|failed\_nodes|Long|0| |
|healthy\_nodes|Long|3| |
|initial\_nodes|Long|0| |
|offline\_nodes|Long|0| |
|removing\_nodes|Long|0| |
|serving\_nodes|Long|3| |
|state|String|active|

 -   -   -   -   -    |
|total\_nodes|Long|3| |
|tee\_config|object| | |
|tee\_enable|Boolean|false|

 -   -    |
|management|object| | |
|enable|Boolean|true|

 -   -    |
|auto\_repair|Boolean|true|

 -   -    |
|upgrade\_config|object| | |
|auto\_upgrade|Boolean|true|

 -   -    |
|surge|Long|5| |
|surge\_percentage|Long|50| |
|max\_unavailable|Long|1|

  |



## 



```
GET /clusters/cc0f87de0b8fb403f86e10e204f83****/nodepools HTTP/1.1 
Content-Type:application/json
```





```
HTTP/1.1 200 OK
Content-Type:application/xml

<nodepools>
    <auto_scaling>
        <eip_bandwidth>5</eip_bandwidth>
        <is_bond_eip>true</is_bond_eip>
        <eip_internet_charge_type>PayByBandwidth</eip_internet_charge_type>
        <enable>true</enable>
        <max_instances>10</max_instances>
        <min_instances>2</min_instances>
        <type>cpu</type>
    </auto_scaling>
    <kubernetes_config>
        <cms_enabled>true</cms_enabled>
        <cpu_policy>none</cpu_policy>
        <labels>
            <key>env</key>
            <value>prod</value>
        </labels>
        <runtime>docker</runtime>
        <runtime_version>19.03.5</runtime_version>
        <taints>
            <key>key</key>
            <value>value</value>
            <effect>NoSchedule</effect>
        </taints>
        <user_data>IyEvYmluL3NoCmVjaG8gIkhlbGxvIEFD****</user_data>
    </kubernetes_config>
    <nodepool_info>
        <created>2020-09-27T19:14:09.156823496+08:00</created>
        <is_default>true</is_default>
        <name>default-nodepool</name>
        <nodepool_id>np615c0e0966124216a0412e10afe0****</nodepool_id>
        <region_id>cn-beijing</region_id>
        <resource_group_id>rg-acfmyvw3wjm****</resource_group_id>
        <type>ess</type>
        <updated>2020-09-27T20:37:46+08:00</updated>
    </nodepool_info>
    <scaling_group>
        <auto_renew>false</auto_renew>
        <auto_renew_period>0</auto_renew_period>
        <data_disks>
            <category>cloud_ssd</category>
            <size>40</size>
            <encrypted>true</encrypted>
            <auto_snapshot_policy_id>sp-2zej1nogjvovnz4z****</auto_snapshot_policy_id>
        </data_disks>
        <image_id>aliyun_2_1903_x64_20G_alibase_20200529.vhd</image_id>
        <instance_charge_type>PostPaid</instance_charge_type>
        <instance_types>ecs.n4.large</instance_types>
        <multi_az_policy>COST_OPTIMIZED</multi_az_policy>
        <on_demand_base_capacity>0</on_demand_base_capacity>
        <on_demand_percentage_above_base_capacity>20</on_demand_percentage_above_base_capacity>
        <spot_instance_pools>5</spot_instance_pools>
        <spot_instance_remedy>false</spot_instance_remedy>
        <compensate_with_on_demand>true</compensate_with_on_demand>
        <period>0</period>
        <period_unit>Month</period_unit>
        <platform>AliyunLinux</platform>
        <ram_policy>KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e****</ram_policy>
        <spot_strategy>NoSpot</spot_strategy>
        <spot_price_limit>
            <instance_type>ecs.c6.large</instance_type>
            <price_limit>0.39</price_limit>
        </spot_price_limit>
        <rds_instances>rm-xxx</rds_instances>
        <scaling_group_id>asg-2ze8n5qw4atggut8****</scaling_group_id>
        <scaling_policy>release</scaling_policy>
        <security_group_id>sg-2ze1iuk12m2sb4c4****</security_group_id>
        <system_disk_category>cloud_efficiency</system_disk_category>
        <system_disk_size>120</system_disk_size>
        <tags>
            <key>env</key>
            <value>prod</value>
        </tags>
        <vswitch_ids>vsw-2ze3ds0mdip0hdz8i****</vswitch_ids>
        <login_password>******</login_password>
        <key_pair>pro-nodepool</key_pair>
    </scaling_group>
    <status>
        <failed_nodes>0</failed_nodes>
        <healthy_nodes>3</healthy_nodes>
        <initial_nodes>0</initial_nodes>
        <offline_nodes>0</offline_nodes>
        <removing_nodes>0</removing_nodes>
        <serving_nodes>3</serving_nodes>
        <state>active</state>
        <total_nodes>3</total_nodes>
    </status>
    <tee_config>
        <tee_enable>false</tee_enable>
    </tee_config>
    <management>
        <enable>true</enable>
        <auto_repair>true</auto_repair>
        <upgrade_config>
            <auto_upgrade>true</auto_upgrade>
            <surge>5</surge>
            <surge_percentage>50</surge_percentage>
            <max_unavailable>1</max_unavailable>
        </upgrade_config>
    </management>
</nodepools>
```



```
HTTP/1.1 200 OK
Content-Type:application/json

{
  "nodepools" : [ {
    "auto_scaling" : {
      "eip_bandwidth" : 5,
      "is_bond_eip" : true,
      "eip_internet_charge_type" : "PayByBandwidth",
      "enable" : true,
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
      "user_data" : "IyEvYmluL3NoCmVjaG8gIkhlbGxvIEFD****"
    },
    "nodepool_info" : {
      "created" : "2020-09-27T19:14:09.156823496+08:00",
      "is_default" : true,
      "name" : "default-nodepool",
      "nodepool_id" : "np615c0e0966124216a0412e10afe0****",
      "region_id" : "cn-beijing",
      "resource_group_id" : "rg-acfmyvw3wjm****",
      "type" : "ess",
      "updated" : "2020-09-27T20:37:46+08:00"
    },
    "scaling_group" : {
      "auto_renew" : false,
      "auto_renew_period" : 0,
      "data_disks" : [ {
        "category" : "cloud_ssd",
        "size" : 40,
        "encrypted" : "true",
        "auto_snapshot_policy_id" : "sp-2zej1nogjvovnz4z****"
      } ],
      "image_id" : "aliyun_2_1903_x64_20G_alibase_20200529.vhd",
      "instance_charge_type" : "PostPaid",
      "instance_types" : [ "ecs.n4.large" ],
      "multi_az_policy" : "COST_OPTIMIZED",
      "on_demand_base_capacity" : 0,
      "on_demand_percentage_above_base_capacity" : 20,
      "spot_instance_pools" : 5,
      "spot_instance_remedy" : false,
      "compensate_with_on_demand" : true,
      "period" : 0,
      "period_unit" : "Month",
      "platform" : "AliyunLinux",
      "ram_policy" : "KubernetesWorkerRole-021dc54f-929b-437a-8ae0-34c24d3e****",
      "spot_strategy" : "NoSpot",
      "spot_price_limit" : [ {
        "instance_type" : "ecs.c6.large",
        "price_limit" : "0.39"
      } ],
      "rds_instances" : [ "rm-xxx" ],
      "scaling_group_id" : "asg-2ze8n5qw4atggut8****",
      "scaling_policy" : "release",
      "security_group_id" : "sg-2ze1iuk12m2sb4c4****",
      "system_disk_category" : "cloud_efficiency",
      "system_disk_size" : 120,
      "tags" : [ {
        "key" : "env",
        "value" : "prod"
      } ],
      "vswitch_ids" : [ "vsw-2ze3ds0mdip0hdz8i****" ],
      "login_password" : "******",
      "key_pair" : "pro-nodepool"
    },
    "status" : {
      "failed_nodes" : 0,
      "healthy_nodes" : 3,
      "initial_nodes" : 0,
      "offline_nodes" : 0,
      "removing_nodes" : 0,
      "serving_nodes" : 3,
      "state" : "active",
      "total_nodes" : 3
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
        "surge_percentage" : 50,
        "max_unavailable" : 1
      }
    }
  } ]
}
```



## 




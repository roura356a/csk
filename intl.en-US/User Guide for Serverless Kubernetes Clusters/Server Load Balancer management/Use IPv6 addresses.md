Use IPv6 addresses 
=======================================

IPv4 addresses are widely used, but the limited number of IPv4 addresses restricts the development of the Internet. Compared with IPv4 addresses, IPv6 addresses are sufficient enough to support more types of devices to access the Internet. Currently, Elastic Container Instance (ECI) supports both IPv4 and IPv6 addresses.

The IPv6 service is in public preview. You can [apply for trial use](https://page.aliyun.com/form/act608662110/index.htm). For more information about the supported regions, see the purchase page.

By default, only IPv4 addresses are assigned to ECIs. To assign an IPv6 address to an ECI, set the Ipv6AddressCount parameter when you create an ECI.

    Ipv6AddressCount = 1



Then, an IPv6 address is automatically assigned to the ECI.

Comparison between IPv4 and IPv6 
-----------------------------------------------------



|            Item             |                                                                                    IPv4                                                                                    |                                                                                                                                                  IPv6                                                                                                                                                  |
|-----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Address length              | Supports addresses with a length of 32 bits, that is, 4 bytes.                                                                                                             | Supports addresses with a length of 128 bits, that is, 16 bytes.                                                                                                                                                                                                                                       |
| Number of addresses         | Provides 2\^32 addresses.                                                                                                                                                  | Provides 2\^128 addresses.                                                                                                                                                                                                                                                                             |
| Address format              | Supports addresses in the xxx.xxx.xxx.xxx format. Each xxx is an integer from 0 to 255. Each x is a decimal digit. The leading zeros can be omitted. Example: 192.168.1.1. | Supports addresses in the xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx:xxxx format. Each x is a hexadecimal digit. The leading zeros can be omitted. You can use double colons (::) once in an address to replace a series of zeros. Example: CDCD:0000:0000:0000:8475:1111:3900:2020=CDCD::8475:1111:3900:2020. |
| Address Resolution Protocol | Uses broadcast Address Resolution Protocol (ARP) request frames to resolve an IP address to a link layer address.                                                          | Uses multicast neighbor solicitation messages to resolve an IP address to a link layer address.                                                                                                                                                                                                        |
| Security                    | Depends its security feature on applications. Therefore, IPv4 cannot guarantee network security at the IP address level.                                                   | Supports IPv6 packet fragmentation to guarantee the confidentiality and data integrity. Therefore, IPv6 can guarantee network security at the IP address level.                                                                                                                                        |
| LAN connection              | Connects to the LAN through network interfaces.                                                                                                                            | Works with an Ethernet adapter to connect to the LAN and can be used across partitions through a virtual Ethernet network.                                                                                                                                                                             |
| Address type                | Supports unicast addresses, multicast addresses, and broadcast addresses.                                                                                                  | Supports unicast addresses, multicast addresses, and anycast addresses.                                                                                                                                                                                                                                |



Limits 
---------------------------

* Only one IPv6 address can be configured for each ECI.

  

* Currently, only ECIs in the China (Hohhot) region support IPv6 addresses.

  

* ECIs created by specifying CPU and memory specifications support IPv6 addresses. ECIs of some Elastic Compute Service (ECS) instance types support IPv6 addresses. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).

  

* IPv6 addresses allow ECIs in a Virtual Private Cloud (VPC) to communicate with each other. To enable the communication over the Internet through an IPv6 address, you must enable IPv6 Internet bandwidth for the IPv6 address on the IPv6 Gateway page in the VPC console. For more information, see [Enable IPv6 Internet bandwidth]().

  




# Configuration DHCP and DNS in Cisco IOS
![Network Topology](https://imgur.com/1Tw8suy.jpg)

Introduction: 

### IP Addresses
| Device                   | Interface    |  IP    | Subnet 
| ------------------------ | -----------  | -----  | ------
| Router1                  | Loopback 0 | 1.1.1.1 | 255.255.255.255
| Router1                  | FastEthernet 0/0 | 198.51.100.2 | 255.255.255.252
| Router1                  | FastEthernet 1/0 | 10.10.1.1 | 255.255.255.0
| Switch 1                 | VLAN 1 |10.10.1.11 | 255.255.255.0
| Switch2                  | VLAN 1 |10.10.1.12| 255.255.255.0

1. First, let's see the IP configuration of each PC and configure them to obtain IP addresses by using DHCP.\
	***C:>ipconfig | C:>ipconfig /ip dhcp***
   
2. See if DHCP server has been configured on Router1\
	 ***Router1#show ip dhcp pool***
   
3. Best practice is to prevent the DHCP server from leasing IP addresses that you do not want to have conflicts later.\
	***Router1(config)#ip dhcp excluded-address 10.10.1.1***
	***Router1(config)#ip dhcp excluded-address 10.10.1.11  10.10.1.12***
   
4. Create a DHCP pool named LAN \
	***Router1(config) #ip dhcp pool LAN***
   
5. Configure the DHCP pool to lease IP addresses. \
	 ***Router1(dhcp-config)#network 10.10.1.0 255.255.255.0***
   
6. Set DHCP pool to configure clients with default gateway. \
	***Router1(dhcp-config)#default-router 10.10.1.1***
   
7. On each PC, refresh the DHCP configurations AND verify IP address information. \
   ***C:>ipconfig /release | C:>ipconfig /renew | C:>ipconfig***
    
9. Verify DHCP binding and pool information on the router. \
	***Router1#show ip dhcp binding | Router1#show ip dhcp pool***

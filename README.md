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
## Step 1: Configure DHCP 
1. First, let's see the IP configuration of each PC and configure them to obtain IP addresses by using DHCP.\
	***C:>ipconfig | C:>ipconfig /ip dhcp***
   
2. See if DHCP server has been configured on Router1\
	 ***Router1#show ip dhcp pool***
   
3. Best practice is to prevent the DHCP server from leasing IP addresses that you do not want to have conflicts later.\
	***Router1(config)#ip dhcp excluded-address 10.10.1.1***\
	***Router1(config)#ip dhcp excluded-address 10.10.1.11  10.10.1.12***
   
4. Create a DHCP pool named LAN \
	***Router1(config) #ip dhcp pool LAN***
   
5. Configure the DHCP pool to lease IP addresses. \
	 ***Router1(dhcp-config)#network 10.10.1.0 255.255.255.0***
   
6. Set DHCP pool to configure clients with default gateway. \
	***Router1(dhcp-config)#default-router 10.10.1.1***
   
7. On each PC, refresh the DHCP configurations AND verify IP address information. \
   ***C:>ipconfig /release | C:>ipconfig /renew | C:>ipconfig***
    
8. Verify DHCP binding and pool information on the router. \
	***Router1#show ip dhcp binding***\
   	***Router1#show ip dhcp pool***


## Step 2: Configure DNS
1. See if there is DNS server configured on the network by pinging to Router1 from PC1 and PC2.\
	***C:>ping Router1***
2. Check for current domain name lookup settings to see if it resolve host names. \
	 ***Router1#show hosts***
3. If not, enable domain name lookup services and host name mappings by using ISP’s DNS server.\
    ***Router1(config)#ip domain-lookup*** \
    ***Router1(config)#ip name-server 203.0.113.1***
5. On Router1, ping the Internet router by using the names isp and isp.example.com. \
	 ***Router1#ping isp | Router1#ping isp.example.com***
6. Configure the DHCP pool to assign a DNS server with Fa 1/0 interface's address of the router. \
	***Router1(config)#ip dhcp pool LAN***\
	***Router1(dhcp-config)#dns-server 10.10.1.1***
7. On PC1 and PC2, release and renew the DHCP leases for each client. \
	***C:>ipconfig /release | C:>ipconfig /renew***
8. On Router 1, start DNS server. Otherwise, PC1 and PC2 are not able to communicate with other hosts on the network by using names. C:>ping isp or isp.example.com will fail because Router1 has been configured only to look up host names itself, not to provide name resolution for other clients yet. \
	***Router1(config)#ip dns server***
	
9.  Those host names have to be mapped to the IP address of 203.0.113.1.\
	***Router1(config)#ip host isp 203.0.113.1***\
	***Router1(config)#ip host isp.example.com 203.0.113.1***
	
	Also, host names router1 and router1.example.com are to be mapped to the address assigned to Router1’s Loopback 0 interface.\
	***Router1(config)#ip host router1 1.1.1.1***\
	***Router1(config)#ip host router1.example.com 1.1.1.1***
    
	Now, pinging the host names such as C:>ping isp and C:> ping router1 will pass.
	
10. Set a default domain on Router1. Configure router's DHCP server to send the default domain value of example.com to DHCP clients along with IP addressing information so that PC1 and PC2 automatically append the domain example.com to DNS lookups for the host names of router1 or isp.\
	***Router1(config)#ip dhcp pool LAN***\
	***Router1(dhcp-config)#domain-name example.com***




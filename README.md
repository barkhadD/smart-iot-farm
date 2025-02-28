enable

configure terminal

## Configure VLANs on Switch1
vlan 10
name Office
exit
vlan 20
name PoS_Shop
exit
vlan 30
name Smart_Farm_IoT
exit
vlan 40
name Public_WiFi
exit

## Assign VLANs to respective ports on Switch1
interface FastEthernet0/1
switchport mode access
switchport access vlan 10
exit

interface FastEthernet0/2
switchport mode access
switchport access vlan 20
exit

interface FastEthernet0/3
switchport mode access
switchport access vlan 10
exit

interface FastEthernet0/4
switchport mode access
switchport access vlan 30
exit

## Configure trunk on Switch1
interface GigabitEthernet0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30,40
exit

## Configure VLANs on Switch2
vlan 10
name Office
exit
vlan 20
name PoS_Shop
exit
vlan 30
name Smart_Farm_IoT
exit
vlan 40
name Public_WiFi
exit

## Assign VLANs to respective ports on Switch2
interface FastEthernet0/1
switchport mode access
switchport access vlan 10
exit

interface FastEthernet0/2
switchport mode access
switchport access vlan 20
exit

interface FastEthernet0/3
switchport mode access
switchport access vlan 30
exit

interface FastEthernet0/4
switchport mode access
switchport access vlan 40
exit

## Configure trunk on Switch2
interface GigabitEthernet0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30,40
exit

## Configure Router1 sub-interfaces
interface GigabitEthernet0/1.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
no shutdown
exit

interface GigabitEthernet0/1.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
no shutdown
exit

interface GigabitEthernet0/2.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
no shutdown
exit

interface GigabitEthernet0/2.40
encapsulation dot1Q 40
ip address 192.168.40.1 255.255.255.0
no shutdown
exit

## Configure DHCP on Router1
ip dhcp pool Office_Network
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
exit

ip dhcp pool PoS_Shop_Network
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
exit

ip dhcp pool Smart_Farm_WiFi
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
exit

ip dhcp pool Public_WiFi
network 192.168.40.0 255.255.255.0
default-router 192.168.40.1
exit

## Configure security settings
interface range FastEthernet0/5 - 24
shutdown
exit

interface FastEthernet0/1
switchport port-security
switchport port-security maximum 2
switchport port-security violation restrict
switchport port-security mac-address sticky
exit

ip access-list standard BLOCK_GUEST
deny 192.168.40.0 0.0.0.255
permit any
exit
interface GigabitEthernet0/1.10
ip access-group BLOCK_GUEST in
exit

line vty 0 4
transport input ssh
exit

## Enable DHCP snooping
ip dhcp snooping
ip dhcp snooping vlan 10,20,30,40
exit

## Enable STP
spanning-tree mode rapid-pvst

## Configure EtherChannel for link aggregation
interface range GigabitEthernet0/1 - 2
channel-group 1 mode active
exit

## Configure QoS
interface FastEthernet0/1
mls qos trust dscp
exit

## Save configuration
write memory
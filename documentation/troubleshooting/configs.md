#save: copy running-config startup-config/ wr
Part 1- Inital setup
en
conf t
hostname
enable?(To check) enable algorithm-type scrypt secret jeremysitlab (9 hasing)  / 
enable secret jeremysitlab (5 hasing) , #do sh run | include secret
username cisco? username cisco algorithm-type scrypt secret ccna (9 hasing) / 
username cisco secret ccna (5 hasing)
line con 0
login local
exec-timeout 30 0
logging synchronous
Do faster,
# R1, Access switches
enable secret jeremysitlab
username cisco secret ccna
line console 0
login local
exec-timeout 30
logging synchronous
# Core, Distribution switches
enable algorithm-type scrypt secret jeremysitlab
username cisco algorithm-type scrypt secret ccna
line console 0
login local
exec-timeout 30
logging synchronous
**Part 2- VLANs, Layer-2 EtherChannel**
# do sh cdp nei
DSW-A1
int g1/0/4-5
channel-group 1 mode desirable
DSW-A2
int g1/0/5
channel-group 1 mode desirable
# do sh ether sum
DSW-A1, DSW-A2
# do sh cdp nei
int range g1/0/1-3
switchport mode trunk
sw nonegotiate
sw trunk native vlan 1000
sw trunk allowed vlan 10,20,40,99
int po1
sw mode trunk
sw nonegotiate
sw trunk nat vlan 1000
sw trunk allowed vlan 10,20,40,99
exit
ASW-A1, ASW-A2, ASW-A3
int range g0/1-2
switchport mode trunk
switchport nonegotiate
switchport trunk native  vlan 1000
switchport trunk allowed vlan 10,20,40,99
DSW-B1, DSW-B2
int range g1/0/1-3
switchport mode trunk
sw nonegotiate
sw trunk native vlan 1000
sw trunk allowed vlan 10,20,30,99
int po1
sw mode trunk
sw nonegotiate
sw trunk nat vlan 1000
sw trunk allowed vlan 10,20,30,99
ASW-B1, ASW-B2, ASW-B3
int range g0/1-2
switchport mode trunk
switchport nonegotiate
switchport trunk native  vlan 1000
switchport trunk allowed vlan 10,20,30,99
\#do sh vtp status
DSW-A1  DSW-B1
vtp version 2
vtp domain JeremysITLab
All Access Switch
vtp mode client
DSW-A1
vlan 10
name PCs
vlan 20
name Phones
vlan 40
name Wi-Fi
vlan 99
name Management
exit
DSW-B1
vlan 10
name PCs
vlan 20
name Phones
vlan 30
name Servers
vlan 99
name Management
exit
ASW-A1
interface f0/1
switchport mode access
switchport access vlan 99
switchport nonegotiate
exit
ASW-B1
interface f0/1
switchport mode access
switchport access vlan 99
switchport nonegotiate
exit
ASW-A2, ASW-A3, ASW-B2
interface f0/1
switchport mode access
switchport access vlan 10
switchport voice vlan 20
switchport nonegotiate
exit
ASW-B3
interface f0/1
switchport mode access
switchport access vlan 30
switchport nonegotiate
ASW-A1
interface f0/2
switchport mode trunk
switchport trunk native vlan 99
switchport trunk allowed vlan 40,99
switchport nonegotiate
DSW-A1, DSW-A2, DSW-B1, DSW-B2
\#sh int status
interface range g1/0/6-24, g1/1/3-4
shutdown
ASW-A1
interface range f0/3 - 24
shutdown
ASW-A2,ASW-A3, ASW-B1, ASW-B2,ASW-B3
interface range f0/2 - 24
shutdown
**Part 3 – IP Addresses, Layer-3 EtherChannel, HSRP**
R1
ISP-A \& ISP-B
int range g0/0/0, g0/1/0
ip add dhcp
no shut
To core switch
interface g0/0
ip address 10.0.0.33 255.255.255.252
no shutdown
interface g0/1
ip address 10.0.0.37 255.255.255.252
shutdown
Loopback0
interface loopback 0
ip address 10.0.0.76 255.255.255.255
\#do sh ip int br
ip routing
CSW1
interface range g1/0/2 - 3
no switchport
channel-group 1 mode desirable
no shutdown
interface port-channel 1
ip address 10.0.0.41 255.255.255.252
no shutdown
CSW2
interface range g1/0/2 - 3
no switchport
channel-group 1 mode desirable
no shutdown
interface port-channel 1
ip address 10.0.0.42 255.255.255.252
no shutdown
\#do sh ether sum
**CSW1** ip config
interface g1/0/1
no switchport
ip address 10.0.0.34 255.255.255.252
no shutdown
interface g1/1/1
no switchport
ip address 10.0.0.45 255.255.255.252
no shutdown
interface g1/1/2
no switchport
ip address 10.0.0.49 255.255.255.252
no shutdown
interface g1/1/3
no switchport
ip address 10.0.0.53 255.255.255.252
no shutdown
interface g1/1/4
no switchport
ip address 10.0.0.57 255.255.255.252
no shutdown
loopback0
interface loopback 0
ip address 10.0.0.77 255.255.255.255
Disable all unused interface
interface range g1/0/4 - 24
shutdown
CSW2 ip Config
interface g1/0/1
no switchport
ip address 10.0.0.38 255.255.255.252
no shutdown
interface g1/1/1
no switchport
ip address 10.0.0.61 255.255.255.252
no shutdown
interface g1/1/2
no switchport
ip address 10.0.0.65 255.255.255.252
no shutdown
interface g1/1/3
no switchport
ip address 10.0.0.69 255.255.255.252
no shutdown
interface g1/1/4
no switchport
ip address 10.0.0.73 255.255.255.252
no shutdown
loopback0
interface loopback 0
ip address 10.0.0.78 255.255.255.255
Disable all unused interface
interface range g1/0/4 - 24
shutdown
DSW-A1
interface g1/1/1
no switchport
ip address 10.0.0.46 255.255.255.252
interface g1/1/2
no switchport
ip address 10.0.0.62 255.255.255.252
interface loopback 0
ip address 10.0.0.79 255.255.255.255
DSW-A2
interface g1/1/1
no switchport
ip address 10.0.0.50 255.255.255.252
interface g1/1/2
no switchport
ip address 10.0.0.66 255.255.255.252
interface loopback 0
ip address 10.0.0.80 255.255.255.255
**DSW-B1**
interface g1/1/1
no switchport
ip address 10.0.0.54 255.255.255.252
interface g1/1/2
no switchport
ip address 10.0.0.70 255.255.255.252
interface loopback 0
ip address 10.0.0.81 255.255.255.255
DSW-B2
interface g1/1/1
no switchport
ip address 10.0.0.58 255.255.255.252
interface g1/1/2
no switchport
ip address 10.0.0.74 255.255.255.252
interface loopback 0
ip address 10.0.0.82 255.255.255.255
Manually Config in SVR1 IP congifs
ASW-A1
interface vlan 99
ip address 10.0.0.4 255.255.255.240
no shutdown
exit
ip default-gateway 10.0.0.1
ASW-A2
interface vlan 99
ip address 10.0.0.5 255.255.255.240
no shutdown
exit
ip default-gateway 10.0.0.1
ASW-A3
interface vlan 99
ip address 10.0.0.6 255.255.255.240
no shutdown
exit
ip default-gateway 10.0.0.1
ASW-B1
interface vlan 99
ip address 10.0.0.20 255.255.255.240
no shutdown
exit
ip default-gateway 10.0.0.17
ASW-B2
interface vlan 99
ip address 10.0.0.21 255.255.255.240
no shutdown
exit
ip default-gateway 10.0.0.17
ASW-B3
interface vlan 99
ip address 10.0.0.22 255.255.255.240
no shutdown
exit
ip default-gateway 10.0.0.17
DSW-A1 (Active Router)
interface vlan 99
ip address 10.0.0.2 255.255.255.240
standby version 2
standby 1 ip 10.0.0.1
standby 1 priority 105
standby 1 preempt
DSW-A2 (Standby Router)
interface vlan 99
ip address 10.0.0.3 255.255.255.240
standby version 2
standby 1 ip 10.0.0.1
DSW-A1 (Active Router)
interface vlan 10
ip address 10.1.0.2 255.255.255.0
standby version 2
standby 2 ip 10.1.0.1
standby 2 priority 105
standby 2 preempt
DSW-A2 (Standby Router)
interface vlan 10
ip address 10.1.0.3 255.255.255.0
standby version 2
standby 2 ip 10.1.0.1
DSW-A1 (Standby Router)
interface vlan 20
ip address 10.2.0.2 255.255.255.0
standby version 2
standby 3 ip 10.2.0.1
DSW-A2 (Active Router)
interface vlan 20
ip address 10.2.0.3 255.255.255.0
standby version 2
standby 3 ip 10.2.0.1
standby 3 priority 105
standby 3 preempt
DSA-A1 (Standby Router)
interface vlan 40
ip address 10.4.0.2 255.255.255.0
standby version 2
standby 4 ip 10.4.0.1
DSW-A2 (Active Router)
interface vlan 40
ip address 10.4.0.3 255.255.255.0
standby version 2
standby 4 ip 10.4.0.1
standby 4 priority 105
standby 4 preempt
DSW-B1 (Active Router)
interface vlan 99
ip address 10.0.0.18 255.255.255.240
standby version 2
standby 1 ip 10.0.0.17
standby 1 priority 105
standby 1 preempt
DSW-B2 (Active Router)
interface vlan 99
ip address 10.0.0.19 255.255.255.240
standby version 2
standby 1 ip 10.0.0.17
DSW-B1 (Active Router)
interface vlan 10
ip address 10.3.0.2 255.255.255.0
standby version 2
standby 2 ip 10.3.0.1
standby 2 priority 105
standby 2 preempt
DSW-B2 (Standby Router)
interface vlan 10
ip address 10.3.0.3 255.255.255.0
standby version 2
standby 2 ip 10.3.0.1
DSW-B1 (Standby Router)
interface vlan 20
ip address 10.4.0.2 255.255.255.0
standby version 2
standby 3 ip 10.4.0.1
DSW-B2 (Active Router)
interface vlan 20
ip address 10.4.0.3 255.255.255.0
standby version 2
standby 3 ip 10.4.0.1
standby 3 priority 105
standby 3 preempt
DSW-B1(Standby Router)
interface vlan 30
ip address 10.5.0.2 255.255.255.0
standby version 2
standby 4 ip 10.5.0.1
DSW-B2 (Active Router)
interface vlan 30
ip address 10.5.0.3 255.255.255.0
standby version 2
standby 4 ip 10.5.0.1
standby 4 priority 105
standby 4 preempt
**Part 4 – Rapid Spanning Tree Protocol**
ALL Access \& Distribution Switch Rapid PVST+ enable
spanning-tree mode rapid-pvst
DSW-A1
spanning-tree vlan 10,99 priority 0
spanning-tree vlan 20,40 priority 4096
DSW-A2
spanning-tree vlan 20,40 priority 0
spanning-tree vlan 10,99 priority 4096
DSW-B1
spanning-tree vlan 10,99 priority 0
spanning-tree vlan 20,30 priority 4096
DSW-B2
spanning-tree vlan 20,30 priority 0
spanning-tree vlan 10,99 priority 4096
ASW-A1 (f0/1 LWAP1, f0/-2 WLC1) WLC! port trunk so do portfast trunk
interface f0/1
spanning-tree portfast
spanning-tree bpduguard enable
interface f0/2
spanning-tree portfast trunk
spanning-tree bpduguard enable
Other 5 switch
interface f0/1
spanning-tree portfast
spanning-tree bpduguard enable
R1- OSPF Config (Interface Mode)
router ospf 1
router-id 10.0.0.76
passive-interface loopback 0
exit
interface g0/0
ip ospf 1 area 0
interface g0/1
ip ospf 1 area 0
interface loopback 0
ip ospf 1 area 0
interface range g0/0 - 1
ip ospf network point-to-point
Core Switches (CSW1 \& CSW2)- OSPF Configs
router ospf 1
router-id 10.0.0.77
passive-interface loopback 0
network 10.0.0.34 0.0.0.0 area 0
network 10.0.0.41 0.0.0.0 area 0
network 10.0.0.45 0.0.0.0 area 0
network 10.0.0.49 0.0.0.0 area 0
network 10.0.0.53 0.0.0.0 area 0
network 10.0.0.57 0.0.0.0 area 0
network 10.0.0.77 0.0.0.0 area 0
int range g1/0/1,g1/1/1-4
ip ospf network point-to-point
router ospf 1
router-id 10.0.0.78
passive-interface loopback 0
network 10.0.0.38 0.0.0.0 area 0
network 10.0.0.42 0.0.0.0 area 0
network 10.0.0.61 0.0.0.0 area 0
network 10.0.0.65 0.0.0.0 area 0
network 10.0.0.69 0.0.0.0 area 0
network 10.0.0.73 0.0.0.0 area 0
network 10.0.0.78 0.0.0.0 area 0
int range g1/0/1,g1/1/1-4
ip ospf network point-to-point
DSW-A1
router ospf 1
router-id 10.0.0.79
passive-interface loopback 0
passive-interface vlan 10
passive-interface vlan 20
passive-interface vlan 40
network 10.0.0.46 0.0.0.0 area 0
network 10.0.0.62 0.0.0.0 area 0
network 10.0.0.79 0.0.0.0 area 0
network 10.0.0.2 0.0.0.0 area 0
network 10.1.0.2 0.0.0.0 area 0
network 10.2.0.2 0.0.0.0 area 0
network 10.6.0.2 0.0.0.0 area 0
exit
interface range g1/1/1 - 2
ip ospf network point-to-point
DSW-A2
router ospf 1
router-id 10.0.0.80
passive-interface loopback 0
passive-interface vlan 10
passive-interface vlan 20
passive-interface vlan 40
network 10.0.0.50 0.0.0.0 area 0
network 10.0.0.66 0.0.0.0 area 0
network 10.0.0.80 0.0.0.0 area 0
network 10.0.0.3 0.0.0.0 area 0
network 10.1.0.3 0.0.0.0 area 0
network 10.2.0.3 0.0.0.0 area 0
network 10.6.0.3 0.0.0.0 area 0
exit
interface range g1/1/1 - 2
ip ospf network point-to-point
DSW-B1
router ospf 1
router-id 10.0.0.81
passive-interface loopback 0
passive-interface vlan 10
passive-interface vlan 20
passive-interface vlan 30
network 10.0.0.54 0.0.0.0 area 0
network 10.0.0.70 0.0.0.0 area 0
network 10.0.0.81 0.0.0.0 area 0
network 10.0.0.18 0.0.0.0 area 0
network 10.3.0.2 0.0.0.0 area 0
network 10.4.0.2 0.0.0.0 area 0
network 10.5.0.2 0.0.0.0 area 0
exit
interface range g1/1/1 - 2
ip ospf network point-to-point
DSW-B2
router ospf 1
router-id 10.0.0.82
passive-interface loopback 0
passive-interface vlan 10
passive-interface vlan 20
passive-interface vlan 30
network 10.0.0.58 0.0.0.0 area 0
network 10.0.0.74 0.0.0.0 area 0
network 10.0.0.82 0.0.0.0 area 0
network 10.0.0.19 0.0.0.0 area 0
network 10.3.0.3 0.0.0.0 area 0
network 10.4.0.3 0.0.0.0 area 0
network 10.5.0.3 0.0.0.0 area 0
exit
interface range g1/1/1 - 2
ip ospf network point-to-point
**R1**
ip route 0.0.0.0 0.0.0.0 203.0.113.1
ip route 0.0.0.0 0.0.0.0 203.0.113.5 2
router ospf 1
default-information originate
**Part 6 – Network Services: DHCP, DNS, NTP, SNMP, Syslog, FTP, SSH, NAT**
**Excluded IP Addresses**
ip dhcp excluded-address 10.0.0.1 10.0.0.10
ip dhcp excluded-address 10.0.0.17 10.0.0.26
ip dhcp excluded-address 10.1.0.1 10.1.0.10
ip dhcp excluded-address 10.2.0.1 10.2.0.10
ip dhcp excluded-address 10.3.0.1 10.3.0.10
ip dhcp excluded-address 10.4.0.1 10.4.0.10
ip dhcp excluded-address 10.5.0.1 10.5.0.10
ip dhcp excluded-address 10.6.0.1 10.6.0.10
pool A-Mfmt
ip dhcp pool A-Mgmt
network 10.0.0.0 255.255.255.240
default-router 10.0.0.1
domain-name jeremysitlab.com
dns-server 10.5.0.4
option 43 ip 10.0.0.7
pool A-PC
ip dhcp pool A-PC
network 10.1.0.0 255.255.255.0
default-router 10.1.0.1
domain-name jeremysitlab.com
dns-server 10.5.0.4
pool A-Phone
ip dhcp pool A-Phone
network 10.2.0.0 255.255.255.0
default-router 10.2.0.1
domain-name jeremysitlab.com
dns-server 10.5.0.4
pool A-Wifi
ip dhcp pool A-WiFi
network 10.6.0.0 255.255.255.0
default-router 10.6.0.1
domain-name jeremysitlab.com
dns-server 10.5.0.4
pool B-Mgmt
ip dhcp pool B-Mgmt
network 10.0.0.16 255.255.255.240
default-router 10.0.0.17
domain-name jeremysitlab.com
dns-server 10.5.0.4
option 43 ip 10.0.0.7
pool B-PC
ip dhcp pool B-PC
network 10.3.0.0 255.255.255.0
default-router 10.3.0.1
domain-name jeremysitlab.com
dns-server 10.5.0.4
pool B-Phone
ip dhcp pool B-Phone
network 10.4.0.0 255.255.255.0
default-router 10.4.0.1
domain-name jeremysitlab.com
dns-server 10.5.0.4
DHCP Relay Agent (ip helper-address)
Office A DSW -A1 \& DSW-A2
interface vlan 10
ip helper-address 10.0.0.76
interface vlan 20
ip helper-address 10.0.0.76
interface vlan 40
ip helper-address 10.0.0.76
interface vlan 99
ip helper-address 10.0.0.76
Office B DSW-B1 \& DSW-B2
interface vlan 10
ip helper-address 10.0.0.76
interface vlan 20
ip helper-address 10.0.0.76
interface vlan 30
ip helper-address 10.0.0.76
interface vlan 99
ip helper-address 10.0.0.76
ip domain-name jeremysitlab.com
ip name-server 10.5.0.4
R1
ntp master 5
ntp server 216.239.35.0
R1 NTP Authentication
ntp authenticate
ntp authentication-key 1 md5 ccna
ntp trusted-key 1
(All Core, Distribution, Access)
ntp authenticate
ntp authentication-key 1 md5 ccna
ntp trusted-key 1
ntp server 10.0.0.76 key 1
SNMP Community String Config
Syslog Server \& Logging Configuration
snmp-server community SNMPSTRING ro
logging host 10.5.0.4
logging trap debugging
logging buffered 8192
R1 FTP Credentials
ip ftp username cisco
ip ftp password cisco
exit
R1
crypto key generate rsa modulus 4096
ip ssh version 2
access-list 1 permit 10.1.0.0 0.0.0.255
line vty 0 15
access-class 1 in
transport input ssh
login local
logging synchronous
(Core Switches: CSW1, CSW2 | Distribution Switches: DSW-A1, DSW-A2, DSW-B1, DSW-B2 |
Access Switches: ASW-A1, ASW-A2, ASW-A3, ASW-B1, ASW-B2, ASW-B3)
crypto key generate rsa modulus 4096
ip ssh version 2
access-list 1 permit 10.1.0.0 0.0.0.255
line vty 0 15
access-class 1 in
transport input ssh
login local
logging synchronous
ip nat inside source static 10.5.0.4 203.0.113.113
interface range g0/0 - 1
ip nat inside
exit
interface g0/0/0
ip nat outside
interface g0/1/0
ip nat outside
**R1**
access-list 2 permit 10.1.0.0 0.0.0.255
access-list 2 permit 10.2.0.0 0.0.0.255
access-list 2 permit 10.3.0.0 0.0.0.255
access-list 2 permit 10.4.0.0 0.0.0.255
access-list 2 permit 10.6.0.0 0.0.0.255
ip nat pool POOL1 203.0.113.200 203.0.113.207 netmask 255.255.255.248
ip nat inside source list 2 pool POOL1 overload
interface g0/0/0
no shutdown
exit
router ospf 1
no default-information originate always
default-information originate
router ospf 1
no default-information originate
default-information originate
R1, CSW1, CSW2, DSW-A1, DSW-A2, DSW-B1, DSW-B2)
no cdp run
lldp run
ASW-A1, ASW-A2, ASW-A3, ASW-B1, ASW-B2, ASW-B3
no cdp run
lldp run
interface f0/1
no lldp transmit
**Part 7 – Security: ACLs and Layer-2 Security Features**
1\. Configure extended ACL OfficeA\_to\_OfficeB where appropriate:
a. Allow ICMP messages from the Office A PCs subnet to the Office B PCs subnet.
b. Block all other traffic from the Office A PCs subnet to the Office B PCs subnet.
c. Allow all other traffic.
d. Apply the ACL according to general best practice for extended ACLs.
DSW-A1 \& DSW-A2
ip access-list extended OfficeA\_to\_OfficeB
permit icmp 10.1.0.0 0.0.0.255 10.3.0.0 0.0.0.255
deny ip 10.1.0.0 0.0.0.255 10.3.0.0 0.0.0.255
permit ip any any
exit
interface vlan 10
ip access-group OfficeA\_to\_OfficeB in
ASW-A1, ASW-B1, ASW-B3
interface f0/1
switchport port-security
switchport port-security mac-address sticky
switchport port-security violation restrict
ASW-A2, ASW-A3, ASW-B2
interface f0/1
switchport port-security
switchport port-security maximum 2
switchport port-security mac-address sticky
switchport port-security violation restrict
a. Enable it for all active VLANs in each LAN.
b. Trust the appropriate ports.
c. Disable insertion of DHCP Option 82.
d. Set a DHCP rate limit of 15 pps on active untrusted ports.
e. Set a higher limit (100 pps) on ASW-A1’s connection to WLC1.
Office A Access Switches (ASW-A1, ASW-A2, ASW-A3):
ip dhcp snooping
ip dhcp snooping vlan 10,20,40,99
no ip dhcp snooping information option
interface range g0/1 - 2
ip dhcp snooping trust
exit
interface f0/1
ip dhcp snooping limit rate 15
exit
interface f0/2
ip dhcp snooping limit rate 100
ip dhcp snooping
ip dhcp snooping vlan 10,20,40,99
no ip dhcp snooping information option
interface range g0/1 - 2
ip dhcp snooping trust
exit
interface f0/1
ip dhcp snooping limit rate 15
exit
ip dhcp snooping
ip dhcp snooping vlan 10,20,40,99
no ip dhcp snooping information option
interface range g0/1 - 2
ip dhcp snooping trust
exit
interface f0/1
ip dhcp snooping limit rate 15
exit
Office B Access Switches (ASW-B1, ASW-B2, ASW-B3):
ip dhcp snooping
ip dhcp snooping vlan 10,20,30,99
no ip dhcp snooping information option
interface range g0/1 - 2
ip dhcp snooping trust
exit
interface f0/1
ip dhcp snooping limit rate 15
exit
&#x20;ASW-A1, ASW-A2, ASW-A3
ip arp inspe
ip arp inspection vlan 10,20,40,99
ip arp inspection validate dst-mac src-mac ip
int range g0/1-2
ip arp inspection trust
exit
ASW-B1, ASW-B2, ASW-B3
ip arp inspe
ip arp inspection vlan 10,20,30,99
ip arp inspection validate  src-mac dst-mac ip
int range g0/1-2
ip arp inspection trust
exit
ipv6 unicast-routing
interface g0/0/0
ipv6 address 2001:db8:a::2/64
exit
interface g0/1/0
ipv6 address 2001:db8:b::2/64
exit
interface g0/0
ipv6 address 2001:db8:a1::/64 eui-64
exit
interface g0/1
ipv6 address 2001:db8:a2::/64 eui-64
CSW1
ipv6 unicast-routing
interface g1/0/1
ipv6 address 2001:db8:a1::/64 eui-64
exit
interface po1
ipv6 enable
CSW2
ipv6 unicast-routing
interface g1/0/1
ipv6 address 2001:db8:a2::/64 eui-64
exit
interface po1
ipv6 enable
R1
ipv6 route ::/0 2001:db8:a::1
ipv6 route ::/0 g0/1/0 2001:db8:b::1 2

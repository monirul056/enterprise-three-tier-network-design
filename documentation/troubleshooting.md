1. SSH RSA Crypto Key & Modulus Bit Verification Failures (CSW1, CSW2, DSW-A1, 
DSW-A2, DSW-B1)
Symptom: Assessment checks flagged crypto key set and modulus bits as incomplete 
despite configuring SSH.
Root Cause: Pre-existing or default 512-bit RSA keys were present, or crypto key 
generate rsa was executed without explicitly enforcing a 4096-bit modulus and saving
to startup-config.
Resolution: Purged legacy keys using crypto key zeroize rsa, regenerated 4096-bit 
RSA keys across all switches, and saved the configuration:
#crypto key zeroize rsa
#ip domain-name jeremysitlab.com
#crypto key generate rsa modulus - 4096
#do write memory

2. Missing Management Default Gateway (ASW-B3)
Symptom: ASW-B3 could not be reached across subnets via SSH/management traffic.
Root Cause: The global default gateway pointing to Office B's Management VLAN (VLAN 
99) HSRP virtual IP was missing from startup-config.
Resolution: Configured the HSRP virtual gateway for Office B Management (10.0.0.17) 
and saved the configuration:
#ip default-gateway 10.0.0.17
#do write memory

3. HSRP Group ID & Preemption Mismatches (DSW-A1, DSW-A2, DSW-B2)
Symptom: On DSW-A1 and DSW-A2, Vlan40 (10.6.0.0/24) had an HSRP group mismatch 
(standby 40 vs. expected standby 4) and missing SVI IP (10.6.0.3) on DSW-A2.
On DSW-B2, Vlan20 (10.4.0.0/24) lacked standby 3 preempt.
Root Cause: Inconsistent HSRP group numbering across SVIs and missing preemption on 
secondary distribution switches.
Resolution: Aligned HSRP group numbers with the lab addressing design and enabled 
preemption:
#On DSW-A1 & DSW-A2 (VLAN 40)
#interface vlan 40
#ip address 10.6.0.3 255.255.255.0   ! (10.6.0.2 on DSW-A1)
#standby 4 ip 10.6.0.1
#standby 4 preempt
#On DSW-B2 (VLAN 20)
#interface vlan 20
#standby 3 preempt

4. Missing DHCP Relay (ip helper-address) on Distribution SVIs (DSW-B1)
Symptom: Wireless/wired clients in Office B failed to receive dynamic IP addresses 
from the central DHCP server (10.0.0.76).
Root Cause: DSW-B1 lacked ip helper-address 10.0.0.76 on active VLAN SVIs to forward
client DHCP broadcasts as unicast packets across routed links.
Resolution: Applied ip helper-address 10.0.0.76 across all Office B SVIs (Vlan10, 
Vlan30, Vlan40, Vlan99).

5. Missing Central DHCP Pool & WLC Dynamic Interface Naming (R1 & WLC1)
Symptom: Wireless clients (Laptop1, Laptop2) failed to obtain IP addresses in the 
10.6.0.0/24 subnet.
Root Cause: R1 was missing the Wi-Fi DHCP scope (10.6.0.0/24) and excluded addresses
(10.6.0.1 - 10.6.0.3).
On WLC1, the dynamic interface name was case-mismatched (Wi-fi instead of Wi-Fi) and
lacked proper VLAN 40 mapping.
Resolution:
Configured the Wi-Fi DHCP pool on R1:
#ip dhcp excluded-address 10.6.0.1 10.6.0.3
#ip dhcp pool Wi-Fi
#network 10.6.0.0 255.255.255.0
#default-router 10.6.0.1
#dns-server 10.5.0.4
#domain-name jeremysitlab.com
Recreated the dynamic interface on WLC1 with exact case-sensitive naming (Wi-Fi), 
mapped it to VLAN 40 (IP: 10.6.0.4, Gateway: 10.6.0.1, DHCP: 10.0.0.76), and bound 
it to the WPA2-PSK WLAN profile.

6. Layer 2 STP Security Hardening (ASW-A1)
Symptom: Assessment flagged missing BPDU Guard protection on an edge access port 
(F0/1 connected to LWAP1).
Root Cause: PortFast was enabled without BPDU Guard, leaving the access port 
vulnerable to rogue switch loops.
Resolution: Enabled BPDU Guard on the edge interface and saved to startup-config:
#interface f0/1
#spanning-tree portfast
#spanning-tree bpduguard enable
#do write memory

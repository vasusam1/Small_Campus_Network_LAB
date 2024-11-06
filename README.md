# Small Campus Network LAB

## Objective

The Detection Lab project aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, generating test telemetry to mimic real-world attack scenarios. This hands-on experience was designed to deepen understanding of network security, attack patterns, and defensive strategies.

The Campus Network Lab project demonstrates the setup and configuration of a campus network with VLANs for department segmentation and a router for inter-VLAN routing to enhance network security and organization.

### Skills Learned

- Network design and topology planning.
- Vlan configuration and management.
- Inter-VLAN routing.
- IP addressing and subnetting.
- Router and switch configuration.
- Network troubleshooting and verification.
- Network security basics.
- Documentation and presentation of technical work.
- Understanding of Layer 2 and 3 concepts.

### Tools Used

- VLAN configuration, inter-VLAN routing, Cisco Packet Tracer, Cisco IOS commands.

## Steps

I wanted to create a network for a campus based on the following requirements:
- The IT section needs 25 hosts.
- The Students need 1500 hosts.
- Faculty needs 200 hosts.
- Faculty also needs IP phones (also 200 hosts).
- There has to be management just incase of remoting into a switch or router for troubleshooting.

Based off of that I have created the subnets as shown below.

| Vlan Name                 | Hosts     | Network Address | Broadcast          | First        | Last         |
| ------------------------- | --------- | --------------- | ------------------ | ------------ | ------------ |
| Vlan 10 (IT)              | 25        | 172.16.10.0     | 172.16.10.31       | 172.16.10.1  | 172.16.10.30 |
| Vlan 20 (Students)        | 1500      | 172.16.0.0      | 172.16.7.255       | 172.16.0.1   | 172.16.7.254 |
| Vlan 30 (Faculty)         | 200       | 172.16.9.0      | 172.16.9.255       | 172.16.9.1   | 172.16.9.254 |
| Vlan 40 (Voice IP phones) | 200       | 172.16.8.0      | 172.16.8.255       | 172.16.8.1   | 172.16.8.254 |
| Vlan 80 (Management)      | 10        | 172.16.10.32    | 172.16.10.47       | 172.16.10.33 | 172.16.10.46 |
| Vlan 99 (Native)          | 4         | 172.16.10.48    | 172.16.10.55       | 172.16.10.49 | 172.16.10.54 |

### Network topology diagram 

![Network Setup and Configuration Project - Topology](https://github.com/user-attachments/assets/2ce25691-d15f-47cc-84ed-a98e26a90573)

### Addressing Table 

| Device Name            | Interface              | IP Address            | Subnet                | Default Gateway |
| ---------------------- | ---------------------- | --------------------- | --------------------- | --------------- |
| R1                     | G0/0/0.10 (IT)         | 172.16.10.1           | 255.255.255.224   /27 | N/A             |
|                        | G0/0/0.20 (Students)   | 172.16.0.1            | 255.255.248.0     /21 | N/A             |
|                        | G0/0/0.30 (Faculty)    | 172.16.9.1            | 255.255.255.0     /24 | N/A             |
|                        | G0/0/0.40 (Voice)      | 172.16.8.1            | 255.255.255.0     /24 | N/A             |
|                        | G0/0/0.80 (Management) | 172.16.10.33          | 255.255.255.240   /28 | N/A             |
|                        | G0/0/0.99 (Native)     | 172.16.10.49          | 255.255.255.248   /29 | N/A             |
| PC-1                   | NIC                    | 172.16.10.30          | 255.255.255.224   /27 | 172.16.10.1     |
| PC-2                   | NIC                    | 172.16.7.254          | 255.255.248.0     /21 | 172.16.0.1      |
| PC-3                   | NIC                    | 172.16.9.253          | 255.255.255.0     /24 | 172.16.9.1      |
| PC-4                   | NIC                    | 172.16.10.29          | 255.255.255.224   /27 | 172.16.10.1     |
| PC-5                   | NIC                    | 172.16.7.253          | 255.255.248.0     /21 | 172.16.0.1      |
| PC-6                   | NIC                    | 172.16.9.252          | 255.255.255.0     /24 | 172.16.9.1      |
| PC-7                   | NIC                    | 172.16.9.254          | 255.255.255.0     /24 | 172.16.9.1      |
| S1                     | VLAN 88                | 172.16.10.34          | 255.255.255.240   /28 | 172.16.10.33    |
| S2                     | VLAN 88                | 172.16.10.35          | 255.255.255.240   /28 | 172.16.10.33    |
| S3                     | VLAN 88                | 172.16.10.36          | 255.255.255.240   /28 | 172.16.10.33    |

### Configuration Guide

#### Create Topology

1. Create the topology according to the topology diagram shown above with cables going to their perscribed ports on switches and routers. With Dashed lines showing Cross-over cables and solid lines showing straight through cables.

#### Router Configuration

1. First I would enter into the router and configure basic settings as shown below.

```bash
Router>enable
Router#config t

Enter configuration commands, one per line.  End with CNTL/Z.

#Create hostname for Router as R1.
Router(config)#hostname R1

#Set Privilaged Exec mode as the following.
R1(config)#enable secret $Cisc0!!PRIV*

#Set User Exec mode as the following with a timeout of 5 mins.
R1(config)#line console 0
R1(config-line)#password $Cisc0!!PRIV*
R1(config-line)#login
R1(config-line)#exec-timeout 5 0
R1(config-line)#exit

#Set VTY lines 0 15 as the following with a timeout of 7 mins with only SSH enabled.
R1(config)#line vty 0 15
R1(config-line)#password $Cisc0!!VTY**
R1(config-line)#login
R1(config-line)#exec-timeout 7 0
R1(config-line)#transport input SSH
R1(config-line)#exit

#Set the login so that the User only has 3 attempts within 2 mins to enter the correct password otherwise it will block the user from accessing the router for 3 mins.
R1(config)#login block-for 180 attempts 3 within 120

#Encrypt all plain-text passwords and set the banner with the appropriate message.
R1(config)#service password-encryption 
R1(config)#banner motd #Authorized Users Only!#
R1(config)#end

#Set clock
R1#clock set 13:00:00 NOV 4 2024


#Save Configuration
R1# copy running-config startup-config
```

2. Shutdown unused interfaces

```bash
R1(config)#interface range gigabitEthernet 0/0/1, gigabitEthernet 0/0/2
R1(config-if-range)#description Unused
R1(config-if-range)#shutdown

R1(config-if-range)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/0/2, changed state to administratively down
```

3. I would then enable sub interfaces for the vlans shown in the addressing table.

3a. Assign GigabitEthernet0/0/0.10 to VLAN 10 (IT).
```bash
R1#config t

# Assign GigabitEthernet0/0/0.10 to VLAN 10 (IT)
R1(config)# interface GigabitEthernet0/0/0.10
R1(config-if)# encapsulation dot1Q 10
R1(config-if)# ip address 172.16.10.1 255.255.255.224
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# 
```
3b. Assign GigabitEthernet0/0/0.20 to VLAN 20 (Students).

```bash
# Assign GigabitEthernet0/0/0.20 to VLAN 20 (Students)
R1(config)# interface GigabitEthernet0/0/0.20
R1(config-if)# encapsulation dot1Q 20
R1(config-if)# ip address 172.16.0.1 255.255.248.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# 
```

3c. Assign GigabitEthernet0/0/0.30 to VLAN 30 (Faculty).

```bash
# Assign GigabitEthernet0/0/0.30 to VLAN 30 (Faculty)
R1(config)# interface GigabitEthernet0/0/0.30
R1(config-if)# encapsulation dot1Q 30
R1(config-if)# ip address 172.16.9.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# 
```

3d. Assign GigabitEthernet0/0/0.40 to VLAN 40 (Voice).

```bash
# Assign GigabitEthernet0/0/0.40 to VLAN 40 (Voice)
R1(config)# interface GigabitEthernet0/0/0.40
R1(config-if)# encapsulation dot1Q 40
R1(config-if)# ip address 172.16.8.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# 
```

3e. Assign GigabitEthernet0/0/0.80 to VLAN 80 (Management) and set as native.

```bash
# Assign GigabitEthernet0/0/0.80 to VLAN 80 (Management)
R1(config)# interface GigabitEthernet0/0/0.80
R1(config-if)# encapsulation dot1Q 80 native
R1(config-if)# ip address 172.16.10.33 255.255.255.240
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# 
```

3f. Assign GigabitEthernet0/0/0.99 to VLAN 99 (Native).

```bash
# Assign GigabitEthernet0/0/0.99 to VLAN 99 (Native)
R1(config)# interface GigabitEthernet0/0/0.99
R1(config-if)# encapsulation dot1Q 99
R1(config-if)# ip address 172.16.10.49 255.255.255.248
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# 
```

3g. Save configuration of sub interfaces GigabitEthernet0/0/0.

```bash
# Save configuration of sub interfaces GigabitEthernet0/0/0
R1(config)# interface GigabitEthernet0/0/0
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# 
```

This is the result

```bash
# ip interfaces created.
R1#show ip interface brief
Interface                 IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0      unassigned      YES unset  up                    up 
GigabitEthernet0/0/0.10   172.16.10.1     YES manual up                    up 
GigabitEthernet0/0/0.20   172.16.0.1      YES manual up                    up 
GigabitEthernet0/0/0.30   172.16.9.1      YES manual up                    up 
GigabitEthernet0/0/0.40   172.16.8.1      YES manual up                    up 
GigabitEthernet0/0/0.80   172.16.10.33    YES manual up                    up 
GigabitEthernet0/0/0.99   172.16.10.49    YES manual up                    up 
GigabitEthernet0/0/1      unassigned      YES unset  administratively down down 
GigabitEthernet0/0/2      unassigned      YES unset  administratively down down 
Vlan1                     unassigned      YES unset  administratively down down
R1#
```

4. Save the running configuration

```bash
R1#copy running-config startup-config
```

result from all of this

```bash
R1#show startup-config
Using 2004 bytes
!
version 15.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
security passwords min-length 13
!
hostname R1
!
login block-for 180 attempts 3 within 120
!
!
enable secret 5 $1$mERr$pXbOzYqH7/9wPQGAu0P2W0
!
!
!
!
!
!
ip cef
no ipv6 cef
!
!
!
!
!
!
!
!
!
!
no ip domain-lookup
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0/0
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/0/0.10
 description IT VLAN
 encapsulation dot1Q 10
 ip address 172.16.10.1 255.255.255.224
!
interface GigabitEthernet0/0/0.20
 description Students VLAN
 encapsulation dot1Q 20
 ip address 172.16.0.1 255.255.248.0
!
interface GigabitEthernet0/0/0.30
 description Faculty VLAN
 encapsulation dot1Q 30
 ip address 172.16.9.1 255.255.255.0
!
interface GigabitEthernet0/0/0.40
 description Voice VLAN
 encapsulation dot1Q 40
 ip address 172.16.8.1 255.255.255.0
!
interface GigabitEthernet0/0/0.80
 description Management VLAN
 encapsulation dot1Q 80 native
 ip address 172.16.10.33 255.255.255.240
!
interface GigabitEthernet0/0/0.99
 description Trunk VLAN
 encapsulation dot1Q 99
 ip address 172.16.10.49 255.255.255.248
!
interface GigabitEthernet0/0/1
 description Unused
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface GigabitEthernet0/0/2
 description Unused
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 description Unused
 no ip address
 shutdown
!
ip classless
!
ip flow-export version 9
!
!
ip access-list extended sl_def_acl
 deny tcp any any eq telnet
 deny tcp any any eq www
 deny tcp any any eq 22
 permit tcp any any eq 22
!
banner motd ^CAuthorized Users Only!^C
!
!
!
!
line con 0
 exec-timeout 5 0
 password 7 08656F471A1A55565328232A1961
 login
!
line aux 0
!
line vty 0 4
 exec-timeout 7 0
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
line vty 5 15
 exec-timeout 7 0
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
!
!
!
end
```

#### Switch Configuration

1. Enter into the switch and configure and save the basic settings as shown below.

```bash
Switch>enable
Switch#config t

Enter configuration commands, one per line.  End with CNTL/Z.

#Create hostname for Switch as S1.
Switch(config)#hostname S1

#Set Privilaged Exec mode as the following.
S1(config)#enable secret $Cisc0!!PRIV*

#Set User Exec mode as the following with a timeout of 5 mins.
S1(config)#line console 0
S1(config-line)#exec-timeout 5 0
S1(config-line)#password $Cisc0!!PRIV*
S1(config-line)#login
S1(config-line)#exit

S1(config)#line vty 0 4
S1(config-line)#exec-timeout 7 0
S1(config-line)#password $Cisc0!!VTY**
S1(config-line)#login
S1(config-line)#transport input ssh
S1(config-line)#exit

#Encrypt all plain-text passwords and set the banner with the appropriate message.
S1(config)#service password-encryption 
S1(config)#banner motd #Authorized Users Only!#
S1(config)#end

#Set clock
S1#clock set 13:00:00 NOV 4 2024

#Save Configuration
S1# copy running-config startup-config
```

2. Create vlan 10 and assigned it the name that was stated in the addressing table. Do the same for the rest of the VLANs in the addressing table (VLANs 20,30,40,80, and 99).
   
```bash
S1#config t

# Create VLAN 10 and assign it the name IT.
S1(config)# vlan 10
S1(config-vlan)# name IT
S1(config)# 
```

Once you have completed that it should look like the following.

```bash
S1#show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8 
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12 
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16 
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20 
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2 
10   IT                               active    
20   Students                         active    
30   Faculty                          active    
40   Voice                            active    
80   Management                       active    
99   Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active  
```

3. Assign an IP address and subnet mask for VLAN 80 according to the addressing table.

```bash
#Assign an IP address and subnet mask for VLAN 80.
S1#configure terminal
S1(config)#interface vlan 80
S1(config-if)#description S1 VLAN address
S1(config-if)#ip address 172.16.10.35 255.255.255.240
S1(config-if)#no shutdown
S1(config-if)#exit
```

4. Assign FastEthernet0/1 and 0/2 as trunk native having only VLANs 10,20,30,40,80 and 99 being allowed. 

```bash
#Access interface range FastEthernet0/1 and 0/2 and set mode as trunk.
S1(config)# interface range FastEthernet0/1-2
S1(config-if)switchport mode trunk

#Set native vlan as 99 with allowed vlans as the following and issue no shutdown.
S1(config-if)switchport trunk native vlan 80
S1(config-if)switchport trunk allowed vlan 10,20,30,40,80,99
S1(config-if)#no shutdown

S1(config-if)#
%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
```

5. Set FastEthernet0/3 as access with 40 as voice and 30 as access.

```bash
#Access interface FastEthernet0/3 and set mode as access.
S1(config)#interface FastEthernet0/3
S1(config-if)switchport mode access

#Set access as vlan 30 and voice as vlan 40 and issue no shutdown.
S1(config-if)switchport access vlan 30
S1(config-if)switchport voice vlan 40
S1(config-if)#no shutdown

S1(config-if)#
%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to up
S1(config-if)#exit
```

vlans should now look like the following.

```bash
S1#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/4, Fa0/5, Fa0/6, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/2
10   IT                               active    
20   Students                         active    
30   Faculty                          active    Fa0/3
40   Voice                            active    Fa0/3
80   Management                       active    
99   Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
30   enet  100030     1500  -      -      -        -    -        0      0
40   enet  100040     1500  -      -      -        -    -        0      0
80   enet  100080     1500  -      -      -        -    -        0      0
99   enet  100099     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
```

Trunks should look like the following.

```bash
S1#show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      80
Fa0/2       on           802.1q         trunking      80
Gig0/1      on           802.1q         trunking      80

Port        Vlans allowed on trunk
Fa0/1       10,20,30,40,80,99
Fa0/2       10,20,30,40,80,99
Gig0/1      10,20,30,40,80,99

Port        Vlans allowed and active in management domain
Fa0/1       10,20,30,40,80,99
Fa0/2       10,20,30,40,80,99
Gig0/1      10,20,30,40,80,99

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       10,20,30,40,80,99
Fa0/2       10,20,30,40,80,99
Gig0/1      10,20,30,40,80,99

S1#
```

6. Shutdown unused interfaces for S1.

```bash
S1(config)#interface range FastEthernet 0/4-24, gigabitEthernet 0/2, vlan 1
S1(config-if-range)#description Unused
S1(config-if-range)#shutdown

S1(config-if-range)#
%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/5, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/6, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/7, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/9, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/10, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/11, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/12, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/13, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/14, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/15, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/16, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/17, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/18, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/19, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/20, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/21, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/22, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/23, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down

S1(config-if-range)#end
S1# 
```

This is the result

```bash
# ip interfaces created.
S1#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
FastEthernet0/1        unassigned      YES manual up                    up 
FastEthernet0/2        unassigned      YES manual up                    up 
FastEthernet0/3        unassigned      YES manual up                    up 
FastEthernet0/4        unassigned      YES manual administratively down down 
FastEthernet0/5        unassigned      YES manual administratively down down 
FastEthernet0/6        unassigned      YES manual administratively down down 
FastEthernet0/7        unassigned      YES manual administratively down down 
FastEthernet0/8        unassigned      YES manual administratively down down 
FastEthernet0/9        unassigned      YES manual administratively down down 
FastEthernet0/10       unassigned      YES manual administratively down down 
FastEthernet0/11       unassigned      YES manual administratively down down 
FastEthernet0/12       unassigned      YES manual administratively down down 
FastEthernet0/13       unassigned      YES manual administratively down down 
FastEthernet0/14       unassigned      YES manual administratively down down 
FastEthernet0/15       unassigned      YES manual administratively down down 
FastEthernet0/16       unassigned      YES manual administratively down down 
FastEthernet0/17       unassigned      YES manual administratively down down 
FastEthernet0/18       unassigned      YES manual administratively down down 
FastEthernet0/19       unassigned      YES manual administratively down down 
FastEthernet0/20       unassigned      YES manual administratively down down 
FastEthernet0/21       unassigned      YES manual administratively down down 
FastEthernet0/22       unassigned      YES manual administratively down down 
FastEthernet0/23       unassigned      YES manual administratively down down 
FastEthernet0/24       unassigned      YES manual administratively down down 
GigabitEthernet0/1     unassigned      YES manual up                    up 
GigabitEthernet0/2     unassigned      YES manual administratively down down 
Vlan1                  unassigned      YES manual administratively down down 
Vlan80                 172.16.10.34    YES manual up                    up
S1#
```

7. Save the running configuration

```bash
S1#copy running-config startup-config
```

result from all of this for S1

```bash
S1#show running-config 
Using 2706 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$pXbOzYqH7/9wPQGAu0P2W0
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
 description trunk line for vlan 10,20,30,80 and 99
 switchport trunk native vlan 80
 switchport trunk allowed vlan 10,20,30,40,80,99
 switchport mode trunk
!
interface FastEthernet0/2
 description trunk line for vlan 10,20,30,80 and 99
 switchport trunk native vlan 80
 switchport trunk allowed vlan 10,20,30,40,80,99
 switchport mode trunk
!
interface FastEthernet0/3
 description Voice and Faculty
 switchport access vlan 30
 switchport mode access
 switchport voice vlan 40
!
interface FastEthernet0/4
 description Unused
 shutdown
!
interface FastEthernet0/5
 description Unused
 shutdown
!
interface FastEthernet0/6
 description Unused
 shutdown
!
interface FastEthernet0/7
 description Unused
 shutdown
!
interface FastEthernet0/8
 description Unused
 shutdown
!
interface FastEthernet0/9
 description Unused
 shutdown
!
interface FastEthernet0/10
 description Unused
 shutdown
!
interface FastEthernet0/11
 description Unused
 shutdown
!
interface FastEthernet0/12
 description Unused
 shutdown
!
interface FastEthernet0/13
 description Unused
 shutdown
!
interface FastEthernet0/14
 description Unused
 shutdown
!
interface FastEthernet0/15
 description Unused
 shutdown
!
interface FastEthernet0/16
 description Unused
 shutdown
!
interface FastEthernet0/17
 description Unused
 shutdown
!
interface FastEthernet0/18
 description Unused
 shutdown
!
interface FastEthernet0/19
 description Unused
 shutdown
!
interface FastEthernet0/20
 description Unused
 shutdown
!
interface FastEthernet0/21
 description Unused
 shutdown
!
interface FastEthernet0/22
 description Unused
 shutdown
!
interface FastEthernet0/23
 description Unused
 shutdown
!
interface FastEthernet0/24
 description Unused
 shutdown
!
interface GigabitEthernet0/1
 switchport trunk native vlan 80
 switchport trunk allowed vlan 10,20,30,40,80,99
 switchport mode trunk
!
interface GigabitEthernet0/2
 description Unused
 shutdown
!
interface Vlan1
 description Unused
 no ip address
 shutdown
!
interface Vlan80
 description S1 VLAN address
 ip address 172.16.10.34 255.255.255.240
!
ip default-gateway 172.16.10.33
!
banner motd ^CAuthorized Users Only!^C
!
!
!
line con 0
 password 7 08656F471A1A55565328232A1961
 login
 exec-timeout 5 0
!
line vty 0 4
 exec-timeout 7 0
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
line vty 5 15
 exec-timeout 7 0
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
!
!
!
!
end
```

#### Switch configuration for S2 and S3.

1. Repeat steps 1 - 3 in the previous switch configuration for S1. For step 4, assign only FastEthernet0/1 as trunk native having only VLANs 10,20,30,40,80 and 99 being allowed. 

2. Set FastEthernet0/3 as access with VLAN 10 should look like the following.

```bash
#Access interface FastEthernet0/3 and set mode as access.
S2(config)#interface FastEthernet0/3
S2(config-if)switchport mode access

#Set access as vlan 10 and issue no shutdown.
S2(config-if)switchport access vlan 10
S2(config-if)#no shutdown

S2(config-if)#
%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to up
S2(config-if)#exit
```

3. Repeat the previous step for FastEthernet0/5, and 0/7 with VLANs 20, and 30 respectively.

vlans should now look like the following for S2.

```bash
S2#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/2, Fa0/4, Fa0/6, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
10   IT                               active    Fa0/3
20   Students                         active    Fa0/5
30   Faculty                          active    Fa0/7
40   Voice                            active    
80   Management                       active    
99   Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
30   enet  100030     1500  -      -      -        -    -        0      0
40   enet  100040     1500  -      -      -        -    -        0      0
80   enet  100080     1500  -      -      -        -    -        0      0
99   enet  100099     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
```

Trunks should look like the following for S2.

```bash
S2#show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      80

Port        Vlans allowed on trunk
Fa0/1       10,20,30,40,80,99

Port        Vlans allowed and active in management domain
Fa0/1       10,20,30,40,80,99

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       10,20,30,40,80,99

S2#
```

vlans should now look like the following for S3.

```bash
S3#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/2, Fa0/4, Fa0/6, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
10   IT                               active    Fa0/3
20   Students                         active    Fa0/5
30   Faculty                          active    Fa0/7
40   Voice                            active    
80   Management                       active    
99   Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------
1    enet  100001     1500  -      -      -        -    -        0      0
10   enet  100010     1500  -      -      -        -    -        0      0
20   enet  100020     1500  -      -      -        -    -        0      0
30   enet  100030     1500  -      -      -        -    -        0      0
40   enet  100040     1500  -      -      -        -    -        0      0
80   enet  100080     1500  -      -      -        -    -        0      0
99   enet  100099     1500  -      -      -        -    -        0      0
1002 fddi  101002     1500  -      -      -        -    -        0      0   
1003 tr    101003     1500  -      -      -        -    -        0      0   
1004 fdnet 101004     1500  -      -      -        ieee -        0      0   
1005 trnet 101005     1500  -      -      -        ibm  -        0      0   

VLAN Type  SAID       MTU   Parent RingNo BridgeNo Stp  BrdgMode Trans1 Trans2
---- ----- ---------- ----- ------ ------ -------- ---- -------- ------ ------

Remote SPAN VLANs
------------------------------------------------------------------------------

Primary Secondary Type              Ports
------- --------- ----------------- ------------------------------------------
```

Trunks should look like the following for S2.

```bash
S2#show interfaces trunk

Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      80

Port        Vlans allowed on trunk
Fa0/1       10,20,30,40,80,99

Port        Vlans allowed and active in management domain
Fa0/1       10,20,30,40,80,99

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       10,20,30,40,80,99

S3#
```

4. Shutdown unused interfaces for S2 and S3.

```bash
S2(config)#interface range FastEthernet 0/2, FastEthernet 0/4, FastEthernet 0/6, FastEthernet 0/8 - 24, GigabitEthernet 0/1 - 2, VLAN 1
S2(config-if-range)#description Unused
S2(config-if-range)#shutdown

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/6, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/9, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/10, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/11, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/12, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/13, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/14, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/15, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/16, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/17, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/18, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/19, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/20, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/21, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/22, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/23, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down

S2(config-if-range)#
%LINK-5-CHANGED: Interface Vlan1, changed state to administratively down

S1(config-if-range)#end
S1# 
```

7. Save the running configuration

```bash
S2#copy running-config startup-config
```

result from all of this for S2


```bash
S2#show startup-config 
Using 2452 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S2
!
enable secret 5 $1$mERr$pXbOzYqH7/9wPQGAu0P2W0
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
 switchport trunk native vlan 80
 switchport trunk allowed vlan 10,20,30,40,80,99
 switchport mode trunk
!
interface FastEthernet0/2
 description Unused
 shutdown
!
interface FastEthernet0/3
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/4
 description Unused
 shutdown
!
interface FastEthernet0/5
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/6
 description Unused
 shutdown
!
interface FastEthernet0/7
 description Faculty computer PC-3
 switchport access vlan 30
 switchport mode access
!
interface FastEthernet0/8
 description Unused
 shutdown
!
interface FastEthernet0/9
 description Unused
 shutdown
!
interface FastEthernet0/10
 description Unused
 shutdown
!
interface FastEthernet0/11
 description Unused
 shutdown
!
interface FastEthernet0/12
 description Unused
 shutdown
!
interface FastEthernet0/13
 description Unused
 shutdown
!
interface FastEthernet0/14
 description Unused
 shutdown
!
interface FastEthernet0/15
 description Unused
 shutdown
!
interface FastEthernet0/16
 description Unused
 shutdown
!
interface FastEthernet0/17
 description Unused
 shutdown
!
interface FastEthernet0/18
 description Unused
 shutdown
!
interface FastEthernet0/19
 description Unused
 shutdown
!
interface FastEthernet0/20
 description Unused
 shutdown
!
interface FastEthernet0/21
 description Unused
 shutdown
!
interface FastEthernet0/22
 description Unused
 shutdown
!
interface FastEthernet0/23
 description Unused
 shutdown
!
interface FastEthernet0/24
 description Unused
 shutdown
!
interface GigabitEthernet0/1
 description Unused
 shutdown
!
interface GigabitEthernet0/2
 description Unused
 shutdown
!
interface Vlan1
 description Unused
 no ip address
 shutdown
!
interface Vlan80
 description S2 VLAN address
 ip address 172.16.10.35 255.255.255.240
!
ip default-gateway 172.16.10.33
!
banner motd ^CAuthorized Users Only!^C
!
!
!
line con 0
 password 7 08656F471A1A55565328232A1961
 login
 exec-timeout 5 0
!
line vty 0 4
 exec-timeout 7 0
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
line vty 5 15
 exec-timeout 7 0
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
!
!
!
!
end
```


result from all of this for S3

```bash
Using 2513 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S3
!
enable secret 5 $1$mERr$pXbOzYqH7/9wPQGAu0P2W0
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
 description Main trunk link to R1, S1, and S2
 switchport trunk native vlan 80
 switchport trunk allowed vlan 10,20,30,40,80,99
 switchport mode trunk
!
interface FastEthernet0/2
 description Unused
 shutdown
!
interface FastEthernet0/3
 switchport access vlan 10
 switchport mode access
!
interface FastEthernet0/4
 description Unused
 shutdown
!
interface FastEthernet0/5
 switchport access vlan 20
 switchport mode access
!
interface FastEthernet0/6
 description Unused
 shutdown
!
interface FastEthernet0/7
 switchport access vlan 30
 switchport mode access
!
interface FastEthernet0/8
 description Unused
 shutdown
!
interface FastEthernet0/9
 description Unused
 shutdown
!
interface FastEthernet0/10
 description Unused
 shutdown
!
interface FastEthernet0/11
 description Unused
 shutdown
!
interface FastEthernet0/12
 description Unused
 shutdown
!
interface FastEthernet0/13
 description Unused
 shutdown
!
interface FastEthernet0/14
 description Unused
 shutdown
!
interface FastEthernet0/15
 description Unused
 shutdown
!
interface FastEthernet0/16
 description Unused
 shutdown
!
interface FastEthernet0/17
 description Unused
 shutdown
!
interface FastEthernet0/18
 description Unused
 shutdown
!
interface FastEthernet0/19
 description Unused
 shutdown
!
interface FastEthernet0/20
 description Unused
 shutdown
!
interface FastEthernet0/21
 description Unused
 shutdown
!
interface FastEthernet0/22
 description Unused
 shutdown
!
interface FastEthernet0/23
 description Unused
 shutdown
!
interface FastEthernet0/24
 description Unused
 shutdown
!
interface GigabitEthernet0/1
 description Unused
 shutdown
!
interface GigabitEthernet0/2
 description Unused
 shutdown
!
interface Vlan1
 description Unused
 no ip address
 shutdown
!
interface Vlan80
 description S3 VLAN address
 ip address 172.16.10.36 255.255.255.240
!
ip default-gateway 172.16.10.33
!
banner motd ^CAuthorized Users Only!^C
!
!
!
line con 0
 password 7 08656F471A1A55565328232A1961
 login
 exec-timeout 5 0
!
line vty 0 4
 exec-timeout 7 0
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
line vty 5 15
 exec-timeout 7 0
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
!
!
!
!
end
```
#### PC IP Address configuration

1. Configure all the PC with their addresses, subnet masks, and default gateways as shown below.

PC-1 configuration

![PC-1](https://github.com/user-attachments/assets/7b95817e-f0be-4ba1-8693-568fe9f55568)

PC-2 configuration

![PC-2](https://github.com/user-attachments/assets/08ba1c7c-753e-45c6-a2af-b57b084c8e0b)

PC-3 configuration

![PC-3](https://github.com/user-attachments/assets/ee378c71-5be2-4895-8bfd-b83411d116be)

PC-4 configuration

![PC-4](https://github.com/user-attachments/assets/a24cc1cc-1da4-4333-9d99-aa3c48205dcf)

PC-5 configuration

![PC-5](https://github.com/user-attachments/assets/79d452f0-b1bb-4f07-bd6a-613372a8a960)

PC-6 configuration

![PC-6](https://github.com/user-attachments/assets/dcd8ac0b-f9c8-4789-90ea-aa23654289b8)

PC-7 configuration

![PC-7](https://github.com/user-attachments/assets/fcb780c2-a575-40e8-8307-74f0a08416c7)

### Verification of configuration

#### Ping tests between PCs, Switches, and Routers.

I have done the ping test between different VLANs from PC-4 including the gateway in the images below.

![Ping_Test_PC-4-1](https://github.com/user-attachments/assets/616d74c2-666f-48ac-98e4-93792cebcaae)
![Ping_Test_PC-4-2](https://github.com/user-attachments/assets/d398c9fb-7697-4a09-a90d-79ebc47dad40)

I have also added a graph showing the rest of the ping tests that I have done for each PC in the table below.

| Device name | Destination Verifying | Destination IP | Check? |
|-------------|-----------------------|----------------|--------|
| PC-1        | PC-2                  | 172.16.7.254   | TRUE   |
|             | PC-3                  | 172.16.9.253   | TRUE   |
|             | PC-4                  | 172.16.10.29   | TRUE   |
|             | PC-5                  | 172.16.7.253   | TRUE   |
|             | PC-6                  | 172.16.9.252   | TRUE   |
|             | PC-7                  | 172.16.9.254   | TRUE   |
|             | S1                    | 172.16.10.34   | TRUE   |
|             | S2                    | 172.16.10.35   | TRUE   |
|             | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| PC-2        | PC-3                  | 172.16.9.253   | TRUE   |
|             | PC-4                  | 172.16.10.29   | TRUE   |
|             | PC-5                  | 172.16.7.253   | TRUE   |
|             | PC-6                  | 172.16.9.252   | TRUE   |
|             | PC-7                  | 172.16.9.254   | TRUE   |
|             | S1                    | 172.16.10.34   | TRUE   |
|             | S2                    | 172.16.10.35   | TRUE   |
|             | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| PC-3        | PC-4                  | 172.16.10.29   | TRUE   |
|             | PC-5                  | 172.16.7.253   | TRUE   |
|             | PC-6                  | 172.16.9.252   | TRUE   |
|             | PC-7                  | 172.16.9.254   | TRUE   |
|             | S1                    | 172.16.10.34   | TRUE   |
|             | S2                    | 172.16.10.35   | TRUE   |
|             | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| PC-4        | PC-5                  | 172.16.7.253   | TRUE   |
|             | PC-6                  | 172.16.9.252   | TRUE   |
|             | PC-7                  | 172.16.9.254   | TRUE   |
|             | S1                    | 172.16.10.34   | TRUE   |
|             | S2                    | 172.16.10.35   | TRUE   |
|             | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| PC-5        | PC-6                  | 172.16.9.252   | TRUE   |
|             | PC-7                  | 172.16.9.254   | TRUE   |
|             | S1                    | 172.16.10.34   | TRUE   |
|             | S2                    | 172.16.10.35   | TRUE   |
|             | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| PC-6        | PC-7                  | 172.16.9.254   | TRUE   |
|             | S1                    | 172.16.10.34   | TRUE   |
|             | S2                    | 172.16.10.35   | TRUE   |
|             | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| PC-7        | S1                    | 172.16.10.34   | TRUE   |
|             | S2                    | 172.16.10.35   | TRUE   |
|             | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| S1          | S2                    | 172.16.10.35   | TRUE   |
|             | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| S2          | S3                    | 172.16.10.36   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |
| S3          | R1 (VLAN 10)          | 172.16.10.1    | TRUE   |
|             | R1 (VLAN 10)          | 172.16.0.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.9.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.8.1     | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.33   | TRUE   |
|             | R1 (VLAN 10)          | 172.16.10.49   | TRUE   |

## Importance of Ping tests

The importance of Ping tests in this regard are for the following:

| Important Points                         | Description                                               |
|------------------------------------------|-----------------------------------------------------------|
| Verifying VLAN isolation.                | Ping tests can help confirm that devices within the same  | 
|                                          | VLAN can communicate while devices in different VLANs     |
|                                          | cannot (unless routing is configured).                    |
| Testing inter-VLAN routing.              | Once inter-VLAN routing is configured on the router,      |
|                                          | ping tests between devices in different VLANs verify      |
|                                          | that the router is properly routing traffic between VLANs.|
| Validating IP addressing and subnetting. | Ping tests help ensure that each device has been assigned |
|                                          | a correct IP address and subnet mask according to its     |
|                                          | VLAN’s subnet.                                            |
| Check router and switch configuration.   | A ping test can reveal potential misconfigurations on the |
|                                          | router’s subinterfaces or switch port assignments.        |
| Troubleshooting connectivity issues.     | Ping tests provide a quick diagnostic tool to identify    |
|                                          | where communication breakdowns occur within the network.  |
| Ensuring network performance.            | Successful pings with low latency confirm that there are  |
|                                          | no delays or connectivity issues in the network path.     |

## Conclusion

This project demonstrated a successful setup of a VLAN-based network that includes inter-VLAN routing and a dedicated Voice VLAN, following a structured configuration approach. The project highlights the following key outcomes:

- Network Segmentation and Traffic Isolation: By configuring VLANs for different departments (IT, Students, Faculty, and Voice), the network effectively isolates traffic between groups. This approach improves security by preventing unauthorized access between departments and enhances network efficiency by containing broadcast domains.
- Inter-VLAN Communication: Configuring a router with subinterfaces enabled controlled communication between VLANs. Devices in different VLANs were able to connect through the router, proving the success of inter-VLAN routing. This setup is essential for scenarios where devices across departments need to access shared resources securely.
- Trunk Link Configuration: Trunk links between the switches and router were configured using a native VLAN to allow traffic from all VLANs to pass over a single link while remaining separated by VLAN tags. This configuration reduces the need for multiple physical links, demonstrating efficient use of network resources.
- Addressing and Subnetting Validation: By assigning IP subnets specific to each VLAN, the network avoids IP conflicts and maintains organized IP addressing. Ping tests confirmed that each device was reachable within its own VLAN, ensuring correct IP addressing and connectivity.
- Voice VLAN and QoS Potential: The dedicated Voice VLAN ensures that voice traffic can be prioritized, setting the foundation for potential QoS implementation. This practice would be valuable in environments where reliable, high-quality voice communication is necessary.
- Scalability and Flexibility: The configuration allows for easy addition of new devices within each VLAN and the creation of new VLANs as needed. This flexibility demonstrates the scalability of the network setup, making it suitable for organizational growth.

Skills and Knowledge Gained

Through this project, I developed a strong understanding of:

- VLAN configuration, trunking, and inter-VLAN routing.
- Configuring routers with subinterfaces for VLAN communication.
- IP addressing, subnetting, and addressing best practices.
- Network testing and troubleshooting with tools like ping to validate connectivity and correct configurations.

This project demonstrates my capability to design, implement, and troubleshoot a structured, scalable network, providing an organized and secure infrastructure ready for real-world applications.



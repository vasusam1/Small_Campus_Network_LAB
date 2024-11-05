# Small Campus Network LAB

## Objective
[Brief Objective - Remove this afterwards]

The Detection Lab project aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, generating test telemetry to mimic real-world attack scenarios. This hands-on experience was designed to deepen understanding of network security, attack patterns, and defensive strategies.

The Campus Network Lab project demonstrates the setup and configuration of a campus network with VLANs for department segmentation and a router for inter-VLAN routing to enhance network security and organization.

### Skills Learned
[Bullet Points - Remove this afterwards]

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
[Bullet Points - Remove this afterwards]

- VLAN configuration, inter-VLAN routing, Cisco Packet Tracer, Cisco IOS commands.

## Steps
drag & drop screenshots here or use imgur and reference them using imgsrc

Every screenshot should have some text explaining what the screenshot is about.
### Network topology diagram 

![Network_Topology_Diagram](https://github.com/user-attachments/assets/d53c10ad-dc32-4207-9007-3cdfefb5cd5b)

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
| S1                     | VLAN 99                | 172.16.10.50          | 255.255.255.248   /29 | 172.16.10.49    |
| S2                     | VLAN 99                | 172.16.10.51          | 255.255.255.248   /29 | 172.16.10.49    |
| S3                     | VLAN 99                | 172.16.10.52          | 255.255.255.248   /29 | 172.16.10.49    |

### Configuration Guide

#### Router Configuration

1. First I would enter into the router and configure basic settings as shown below.

```bash
Router>enable
Router#config t

Enter configuration commands, one per line.  End with CNTL/Z.

Router(config)#hostname R1

R1(config)#enable secret $Cisc0!!PRIV*

R1(config)#line console 0
R1(config-line)#password $Cisc0!!PRIV*
R1(config-line)#login
R1(config-line)#exit

R1(config)#line vty 0 4
R1(config-line)#password $Cisc0!!VTY**
R1(config-line)#login
R1(config-line)#transport input SSH
R1(config-line)#exit

R1(config)#service password-encryption 
R1(config)#banner motd #Authorized Users Only!#
R1(config)#end

R1#clock set 13:00:00 NOV 4 2024
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

3e. Assign GigabitEthernet0/0/0.80 to VLAN 80 (Management).

```bash
# Assign GigabitEthernet0/0/0.80 to VLAN 80 (Management)
R1(config)# interface GigabitEthernet0/0/0.80
R1(config-if)# encapsulation dot1Q 80
R1(config-if)# ip address 172.16.10.33 255.255.255.240
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# 
```

3f. Assign GigabitEthernet0/0/0.99 to VLAN 99 (Native).

```bash
# Assign GigabitEthernet0/0/0.99 to VLAN 99 (Native)
R1(config)# interface GigabitEthernet0/0/0.80
R1(config-if)# encapsulation dot1Q 99 native
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
Using 1729 bytes
!
version 15.4
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
security passwords min-length 13
!
hostname R1
!
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
 encapsulation dot1Q 80
 ip address 172.16.10.33 255.255.255.240
!
interface GigabitEthernet0/0/0.99
 description Trunk VLAN
 encapsulation dot1Q 99 native
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
!
banner motd ^CAuthorized Users Only!^C
!
!
!
!
line con 0
 password 7 08656F471A1A55565328232A1961
 login
!
line aux 0
!
line vty 0 4
 password 7 08656F471A1A5556533D383D6061
 login
 transport input ssh
line vty 5 15
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

Switch(config)#hostname S1

S1(config)#enable secret $Cisc0!!PRIV*

S1(config)#line console 0
S1(config-line)#password $Cisc0!!PRIV*
S1(config-line)#login
S1(config-line)#exit

S1(config)#line vty 0 4
S1(config-line)#password $Cisc0!!VTY**
S1(config-line)#login
S1(config-line)#transport input SSH
S1(config-line)#exit

S1(config)#service password-encryption 
S1(config)#banner motd #Authorized Users Only!#
S1(config)#end

S1(config)#end

S1#clock set 13:00:00 NOV 4 2024

S1# copy running-config startup-config
```

3. Create vlan 10 and assigned it the name that was stated in the addressing table. Do the same for the rest of the VLANs in the addressing table (VLANs 20,30,40,80, and 99).
   
```bash
R1#config t

# Create VLAN 10 and assign it the name IT.
R1(config)# vlan 10
R1(config-vlan)# name IT
R1(config)# 
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

2. Shutdown unused interfaces

```bash
R1(config)#interface range FastEthernet 0/4-24, gigabitEthernet 0/2, vlan 1
R1(config-if-range)#description Unused
R1(config-if-range)#shutdown

R1(config-if-range)#
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

R1(config-if-range)#end 
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
Vlan99                 172.16.10.50    YES manual up                    up
S1#
```

4. Save the running configuration

```bash
S1#copy running-config startup-config
```

result from all of this

```bash
S1#show startup-config 
Using 2604 bytes
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
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,80,99
 switchport mode trunk
!
interface FastEthernet0/2
 description trunk line for vlan 10,20,30,80 and 99
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,80,99
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
 switchport trunk native vlan 99
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
interface Vlan99
 ip address 172.16.10.50 255.255.255.248
!
ip default-gateway 172.16.10.49
!
banner motd ^CAuthorized Users Only!^C
!
!
!
line con 0
 password 7 08656F471A1A55565328232A1961
 login
!
line vty 0 4
 password 7 08656F471A1A5556533D383D6061
 login
line vty 5 15
 password 7 08656F471A1A5556533D383D6061
 login
!
!
!
!
end
```

*Ref 1: Network Diagram*

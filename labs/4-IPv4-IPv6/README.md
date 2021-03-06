### IPv4/IPv6

**Цель: Задокументировать и настроить адресное пространство сети в г. Москва и г. Санкт-Петербург**  

**Схема сети г. Москва**  
![](https://github.com/merkelev/neteng/blob/main/labs/4-IPv4-IPv6/NET-MOSCOW2.png)  

Добавил линки между R12 и R13. Линки добавил для избыточности когда будет динамическая маршрутизация в сети.     

Для г. Москва использованы адреса IPv4 - 172.18.0.0/16 и IPv6 - 2001:DB8:ACAD:2::0/64  

**Таблица адресов IPv4 г. Москва**  
| Device   | Interface     | IP Address | Subnet Mask | Default Gateway | Description  |
| -------- | ------------- | --------   | --------    | --------        | ------------ |
| R14      | Et0/3         | 172.18.0.1  | 255.255.255.252 |            | to R19:et0/0 |
|          | Et0/0         | 172.18.0.5  | 255.255.255.252 |            | to R12:et0/2 |
|          | Et0/1         | 172.18.0.9  | 255.255.255.252 |            | to R13:et0/3 |
| R19      | Et0/0         | 172.18.0.2  | 255.255.255.252 | 172.18.0.1 | to R14:et0/3 |
| R15      | Et0/0         | 172.18.0.17 | 255.255.255.252 |            | to R13:et0/2 |
|          | Et0/1         | 172.18.0.13 | 255.255.255.252 |            | to R12:et0/3 |
|          | Et0/3         | 172.18.0.21 | 255.255.255.252 |            | to R20:et0/0 | 
| R20      | Et0/0         | 172.18.0.22 | 255.255.255.252 | 172.18.0.21| to R15:et0/3 |
| R12      | Et0/0         |             |                 |            | to SW4:et1/0 |
|          | Et0/0.9       | 172.18.9.2  | 255.255.255.128 |            |              |
|          | Et0/0.12      | 172.18.12.2 | 255.255.255.128 |            |              |
|          | Et0/0.14      | 172.18.14.2 | 255.255.255.128 |            |              |
|          | Et0/1         | 172.18.1.5  | 255.255.255.252 |            | to R13:et0/1 |
|          | Et0/3         | 172.18.0.14 | 255.255.255.252 |            | to R15:et0/1 |
|          | Et0/2         | 172.18.0.6  | 255.255.255.252 |            | to R14:et0/0 |
| R13      | Et0/0         |             |                 |            | to SW5:et1/0 |     
|          | Et0/0.9       | 172.18.9.3  | 255.255.255.128 |            |              |
|          | Et0/0.12      | 172.18.12.3 | 255.255.255.128 |            |              |
|          | Et0/0.14      | 172.18.14.3 | 255.255.255.128 |            |              |
|          | Et0/1         | 172.18.0.25 | 255.255.255.252 |            | to R12:et0/1 |
|          | Et0/3         | 172.18.0.10 | 255.255.255.252 |            | to R14:et0/1 |
|          | Et0/2         | 172.18.0.18 | 255.255.255.252 |            | to R15:et0/0 | 
| SW4      | VLAN9         | 172.18.9.6  | 255.255.255.128 |            |              |
| SW5      | VLAN9         | 172.18.9.5  | 255.255.255.128 |            |              |
| SW3      | VLAN9         | 172.18.9.7  | 255.255.255.128 |            |              |
| SW2      | VLAN9         | 172.18.9.8  | 255.255.255.128 |            |              |
| VPC1     | NIC           | 172.18.12.10| 255.255.255.128 | 172.18.12.1|              |
| VPC7     | NIC           | 172.18.14.10| 255.255.255.128 | 172.18.14.1|              |

**Таблица адресов IPv6 г. Москва**  
| Device   | Interface     |  IPv6 Address          |Prefix| Default Gateway        |  Description | 
| -------- | ------------- |   --------             | ---  | --------               |  ------------|
| R14      | Et0/3         | 2001:DB8:ACAD:2::18:2  | /126 |                        | to R19:et0/0 |
|          | Et0/0         | 2001:DB8:ACAD:2::18:5  | /126 |                        | to R12:et0/2 |
|          | Et0/1         | 2001:DB8:ACAD:2::18:9  | /126 |                        | to R13:et0/3 |
| R19      | Et0/0         | 2001:DB8:ACAD:2::18:1  | /126 | 2001:DB8:ACAD:2::18:2  | to R14:et0/3 |
| R15      | Et0/0         | 2001:DB8:ACAD:2::18:12 | /126 |                        | to R13:et0/2 |
|          | Et0/1         | 2001:DB8:ACAD:2::18:19 | /126 |                        | to R12:et0/3 |
|          | Et0/3         | 2001:DB8:ACAD:2::18:15 | /126 |                        | to R20:et0/0 | 
| R20      | Et0/0         | 2001:DB8:ACAD:2::18:16 | /126 | 2001:DB8:ACAD:2::18:15 | to R15:et0/3 |
| R12      | Et0/0         |                        |      |                        | to SW4:et1/0 |
|          | Et0/0.9       | 2001:DB8:ACAD:2::9:1   | /120 |                        |              |
|          |               | 2001:DB8:ACAD:2::9:2   | /120 |                        |              |
|          | Et0/0.12      | 2001:DB8:ACAD:2::12:1  | /120 |                        |              |
|          |               | 2001:DB8:ACAD:2::12:2  | /120 |                        |              |
|          | Et0/0.14      | 2001:DB8:ACAD:2::14:1  | /120 |                        |              |
|          |               | 2001:DB8:ACAD:2::14:2  | /120 |                        |              |
|          | Et0/1         | 2001:DB8:ACAD:2::18:D  | /126 |                        | to R13:et0/1 |
|          | Et0/3         | 2001:DB8:ACAD:2::18:1A | /126 |                        | to R15:et0/1 |
|          | Et0/2         | 2001:DB8:ACAD:2::18:6  | /126 |                        | to R14:et0/0 |
| R13      | Et0/0         |                        |      |                        | to SW5:et1/0 |     
|          | Et0/0.9       | 2001:DB8:ACAD:2::9:1   | /120 |                        |              |
|          |               | 2001:DB8:ACAD:2::9:3   | /120 |                        |              |
|          | Et0/0.12      | 2001:DB8:ACAD:2::12:1  | /120 |                        |              |
|          |               | 2001:DB8:ACAD:2::12:3  | /120 |                        |              |
|          | Et0/0.14      | 2001:DB8:ACAD:2::14:1  | /120 |                        |              |
|          |               | 2001:DB8:ACAD:2::14:3  | /120 |                        |              |
|          | Et0/1         | 2001:DB8:ACAD:2::18:E  | /126 |                        | to R12:et0/1 |
|          | Et0/3         | 2001:DB8:ACAD:2::18:A  | /126 |                        | to R14:et0/1 |
|          | Et0/2         | 2001:DB8:ACAD:2::18:11 | /126 |                        | to R15:et0/0 | 
| SW4      | VLAN9         | 2001:DB8:ACAD:2::9:4   | /120 |                        |              |
| SW5      | VLAN9         | 2001:DB8:ACAD:2::9:5   | /120 |                        |              |
| SW3      | VLAN9         | 2001:DB8:ACAD:2::9:6   | /120 |                        |              |
| SW2      | VLAN9         | 2001:DB8:ACAD:2::9:7   | /120 |                        |              |
| VPC7     | NIC           | 2001:db8:acad:2::14:6  | /120 | 2001:DB8:ACAD:2::14:1  |              |
| VPC1     | NIC           | 2001:db8:acad:2::12:6  | /120 | 2001:DB8:ACAD:2::12:1  |              |

**Таблица VLAN г. Москва**  
| VLAN | Name | Interface Assigned  |
| ---- | ----- | ------------------ |
| 66   | NATIVE| N/A                |
| 9    | MNGM  | SW4 VLAN9, SW5 VLAN9, SW3 VLAN9, SW2 VLAN9 |
| 12   | ACC   | SW3 Et0/2                 |
| 14   | SKLAD | SW2 Et0/2                 |
| 54   | PRK-LOT | SW4 Et1/1, Et1/2, Et1/3 |
|      |         | SW5 Et1/1, Et1/2, Et1/3 |
|      |         | SW3 Et1/0, Et1/1, Et1/2, Et1/3 |
|      |         | SW2 Et1/0, Et1/1, Et1/2, Et1/3 |

**На коммутаторах (SW2, SW3, SW4, SW5) настроен RSTP. Коммутатор SW2 являеется корневым.**  

**Конфигурация коммутатора SW3**  
```
Current configuration : 1810 bytes
!
! Last configuration change at 16:30:33 +07 Thu Apr 15 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW3
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
vtp mode transparent
!
no ip domain-lookup
ip cef
no ipv6 cef
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
vlan 9
 name MNGM
!
vlan 12
 name ACC
!
vlan 14
 name SKLAD
!
vlan 54
 name PRK-LOT
!
interface Ethernet0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/2
 switchport access vlan 12
 switchport mode access
!
interface Ethernet0/3
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet1/0
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan9
 ip address 172.18.9.7 255.255.255.128
 shutdown
 ipv6 address 2001:DB8:ACAD:2::9:6/120
!
end
```

**Конфигурация коммутатора SW2**  
```
Current configuration : 1721 bytes
!
! Last configuration change at 16:31:38 +07 Thu Apr 15 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW2
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
vtp mode transparent
!
no ip domain-lookup
ip cef
no ipv6 cef
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
vlan 9,12,14
!
vlan 54
 name PRK-LOT
!
interface Ethernet0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/2
 switchport access vlan 14
 switchport mode access
!
interface Ethernet0/3
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet1/0
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan9
 ip address 172.18.9.8 255.255.255.128
 shutdown
 ipv6 address 2001:DB8:ACAD:2::9:7/120
!
end
```

**Конфигурация коммутора SW4**  
```
Current configuration : 2186 bytes
!
! Last configuration change at 16:38:39 +07 Thu Apr 15 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW4
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
vtp mode transparent
!
no ip domain-lookup
ip cef
no ipv6 cef
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
vlan 9
 name MNGM
!
vlan 12
 name ACC
!
vlan 14
 name SKLAD
!
vlan 54
 name PRK-LOT
!
vlan 66
 name NAT
!
interface Port-channel1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
 channel-group 1 mode active
!
interface Ethernet0/3
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 channel-group 1 mode active
!
interface Ethernet1/0
 description TO-R12-ET0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport mode trunk
 duplex auto
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan9
 ip address 172.18.9.6 255.255.255.128
 standby version 2
 ipv6 address 2001:DB8:ACAD:2::9:4/120
!
end
```

**SW5**  
```
Current configuration : 2151 bytes
!
! Last configuration change at 16:29:30 +07 Thu Apr 15 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW5
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
vtp mode transparent
!
no ip domain-lookup
ip cef
no ipv6 cef
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
vlan 9,12,14
!
vlan 54
 name PRK-LOT
!
vlan 66
 name NATIVE
!
interface Port-channel1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
 channel-group 1 mode active
!
interface Ethernet0/3
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 channel-group 1 mode active
!
interface Ethernet1/0
 description TO-R13-ET0/0
 switchport access vlan 54
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport mode trunk
 duplex auto
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan9
 ip address 172.18.9.5 255.255.255.128
 ipv6 address 2001:DB8:ACAD:2::9:5/120
!
end
```

**На маршрутизаторах R12 и R13 настроен HSRP для IPv4 и IPv6.**  
![](https://github.com/merkelev/neteng/blob/main/labs/4-IPv4-IPv6/HSRP-R12.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/4-IPv4-IPv6/HSRP-R13.png)  

**Конфигурации маршрутизатора R12**  
```
Current configuration : 2247 bytes
!
! Last configuration change at 16:36:47 +07 Thu Apr 15 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R12
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
track 1 interface Ethernet0/2 line-protocol
!
interface Ethernet0/0
 description TO-SW4-ET1/0
 no ip address
!
interface Ethernet0/0.9
 encapsulation dot1Q 9
 ip address 172.18.9.2 255.255.255.128
 standby version 2
 standby 9 ip 172.18.9.1
 standby 9 priority 120
 standby 209 ipv6 2001:DB8:ACAD:2::9:1/120
 standby 209 priority 120
 ipv6 address 2001:DB8:ACAD:2::9:2/120
!
interface Ethernet0/0.12
 encapsulation dot1Q 12
 ip address 172.18.12.2 255.255.255.128
 standby version 2
 standby 12 ip 172.18.12.1
 standby 12 priority 120
 standby 212 ipv6 2001:DB8:ACAD:2::12:1/120
 standby 212 priority 120
 ipv6 address 2001:DB8:ACAD:2::12:2/120
!
interface Ethernet0/0.14
 encapsulation dot1Q 14
 ip address 172.18.14.2 255.255.255.128
 standby version 2
 standby 14 ip 172.18.14.1
 standby 14 priority 120
 standby 214 ipv6 2001:DB8:ACAD:2::14:1/120
 standby 214 priority 120
 ipv6 address 2001:DB8:ACAD:2::14:2/120
!
interface Ethernet0/1
 description TO-SW5-ET0/2
 ip address 172.18.0.26 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:D/126
!
interface Ethernet0/2
 description TO-R14-ET0/0
 ip address 172.18.0.6 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:6/126
!
interface Ethernet0/3
 description TO-R15-ET0/1
 ip address 172.18.0.14 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:1A/126
!
interface Ethernet1/0
 no ip address
 shutdown
!
interface Ethernet1/1
 no ip address
 shutdown
!
interface Ethernet1/2
 no ip address
 shutdown
!
interface Ethernet1/3
 no ip address
 shutdown
!
end
```  
**Таблица маршрутизации IPv4 на R12**  
```
Gateway of last resort is not set
      172.18.0.0/16 is variably subnetted, 12 subnets, 3 masks
C        172.18.0.4/30 is directly connected, Ethernet0/2
L        172.18.0.6/32 is directly connected, Ethernet0/2
C        172.18.0.12/30 is directly connected, Ethernet0/3
L        172.18.0.14/32 is directly connected, Ethernet0/3
C        172.18.0.24/30 is directly connected, Ethernet0/1
L        172.18.0.26/32 is directly connected, Ethernet0/1
C        172.18.9.0/25 is directly connected, Ethernet0/0.9
L        172.18.9.2/32 is directly connected, Ethernet0/0.9
C        172.18.12.0/25 is directly connected, Ethernet0/0.12
L        172.18.12.2/32 is directly connected, Ethernet0/0.12
C        172.18.14.0/25 is directly connected, Ethernet0/0.14
L        172.18.14.2/32 is directly connected, Ethernet0/0.14
```  
**Таблица маршрутизации IPv6 на R12**  
```
C   2001:DB8:ACAD:2::9:0/120 [0/0]
     via Ethernet0/0.9, directly connected
L   2001:DB8:ACAD:2::9:1/128 [0/0]
     via Ethernet0/0.9, receive
L   2001:DB8:ACAD:2::9:2/128 [0/0]
     via Ethernet0/0.9, receive
C   2001:DB8:ACAD:2::12:0/120 [0/0]
     via Ethernet0/0.12, directly connected
L   2001:DB8:ACAD:2::12:1/128 [0/0]
     via Ethernet0/0.12, receive
L   2001:DB8:ACAD:2::12:2/128 [0/0]
     via Ethernet0/0.12, receive
C   2001:DB8:ACAD:2::14:0/120 [0/0]
     via Ethernet0/0.14, directly connected
L   2001:DB8:ACAD:2::14:1/128 [0/0]
     via Ethernet0/0.14, receive
L   2001:DB8:ACAD:2::14:2/128 [0/0]
     via Ethernet0/0.14, receive
C   2001:DB8:ACAD:2::18:4/126 [0/0]
     via Ethernet0/2, directly connected
L   2001:DB8:ACAD:2::18:6/128 [0/0]
     via Ethernet0/2, receive
C   2001:DB8:ACAD:2::18:C/126 [0/0]
     via Ethernet0/1, directly connected
L   2001:DB8:ACAD:2::18:D/128 [0/0]
     via Ethernet0/1, receive
C   2001:DB8:ACAD:2::18:18/126 [0/0]
     via Ethernet0/3, directly connected
L   2001:DB8:ACAD:2::18:1A/128 [0/0]
     via Ethernet0/3, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурация маршрутизатора R13**  
```
Current configuration : 2172 bytes
!
! Last configuration change at 16:36:55 +07 Thu Apr 15 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R13
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-SW5-ET1/0
 no ip address
!
interface Ethernet0/0.9
 encapsulation dot1Q 9
 ip address 172.18.9.3 255.255.255.128
 standby version 2
 standby 9 ip 172.18.9.1
 standby 9 preempt
 standby 209 ipv6 2001:DB8:ACAD:2::9:1/120
 standby 209 preempt
 ipv6 address 2001:DB8:ACAD:2::9:3/120
!
interface Ethernet0/0.12
 encapsulation dot1Q 12
 ip address 172.18.12.3 255.255.255.128
 standby version 2
 standby 12 ip 172.18.12.1
 standby 12 preempt
 standby 212 ipv6 2001:DB8:ACAD:2::12:1/120
 standby 212 preempt
 ipv6 address 2001:DB8:ACAD:2::12:3/120
!
interface Ethernet0/0.14
 encapsulation dot1Q 14
 ip address 172.18.14.3 255.255.255.128
 standby version 2
 standby 14 ip 172.18.14.1
 standby 14 preempt
 standby 214 ipv6 2001:DB8:ACAD:2::14:1/120
 standby 214 preempt
 ipv6 address 2001:DB8:ACAD:2::14:3/120
!
interface Ethernet0/1
 description TO-SW4-ET0/2
 ip address 172.18.0.25 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:E/126
!
interface Ethernet0/2
 description TO-R15-ET0/0
 ip address 172.18.0.18 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:11/126
!
interface Ethernet0/3
 description TO-R14-ET0/1
 ip address 172.18.0.10 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:A/126
!
interface Ethernet1/0
 no ip address
 shutdown
!
interface Ethernet1/1
 no ip address
 shutdown
!
interface Ethernet1/2
 no ip address
 shutdown
!
interface Ethernet1/3
 no ip address
 shutdown
!
end
```  
**Таблица маршрутизации IPv4 на R13**  
```
Gateway of last resort is not set
      172.18.0.0/16 is variably subnetted, 12 subnets, 3 masks
C        172.18.0.8/30 is directly connected, Ethernet0/3
L        172.18.0.10/32 is directly connected, Ethernet0/3
C        172.18.0.16/30 is directly connected, Ethernet0/2
L        172.18.0.18/32 is directly connected, Ethernet0/2
C        172.18.0.24/30 is directly connected, Ethernet0/1
L        172.18.0.25/32 is directly connected, Ethernet0/1
C        172.18.9.0/25 is directly connected, Ethernet0/0.9
L        172.18.9.3/32 is directly connected, Ethernet0/0.9
C        172.18.12.0/25 is directly connected, Ethernet0/0.12
L        172.18.12.3/32 is directly connected, Ethernet0/0.12
C        172.18.14.0/25 is directly connected, Ethernet0/0.14
L        172.18.14.3/32 is directly connected, Ethernet0/0.14
```  
**Таблица маршрутизации IPv6 на R13**  
```
C   2001:DB8:ACAD:2::9:0/120 [0/0]
     via Ethernet0/0.9, directly connected
L   2001:DB8:ACAD:2::9:3/128 [0/0]
     via Ethernet0/0.9, receive
C   2001:DB8:ACAD:2::12:0/120 [0/0]
     via Ethernet0/0.12, directly connected
L   2001:DB8:ACAD:2::12:3/128 [0/0]
     via Ethernet0/0.12, receive
C   2001:DB8:ACAD:2::14:0/120 [0/0]
     via Ethernet0/0.14, directly connected
L   2001:DB8:ACAD:2::14:3/128 [0/0]
     via Ethernet0/0.14, receive
C   2001:DB8:ACAD:2::18:8/126 [0/0]
     via Ethernet0/3, directly connected
L   2001:DB8:ACAD:2::18:A/128 [0/0]
     via Ethernet0/3, receive
C   2001:DB8:ACAD:2::18:C/126 [0/0]
     via Ethernet0/1, directly connected
L   2001:DB8:ACAD:2::18:E/128 [0/0]
     via Ethernet0/1, receive
C   2001:DB8:ACAD:2::18:10/126 [0/0]
     via Ethernet0/2, directly connected
L   2001:DB8:ACAD:2::18:11/128 [0/0]
     via Ethernet0/2, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурация маршрутизатора R14**  
```
Current configuration : 1168 bytes
!
! Last configuration change at 16:37:13 +07 Thu Apr 15 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R14
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R12-ET0/2
 ip address 172.18.0.5 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:5/126
!
interface Ethernet0/1
 description TO-R13-ET0/3
 ip address 172.18.0.9 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:9/126
!
interface Ethernet0/2
 no ip address
!
interface Ethernet0/3
 description TO-R19-ET0/0
 ip address 172.18.0.1 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:2/126
!
end
```  

**Таблица маршрутизации IPv4 на R14**  
```
Gateway of last resort is not set
      172.18.0.0/16 is variably subnetted, 6 subnets, 2 masks
C        172.18.0.0/30 is directly connected, Ethernet0/3
L        172.18.0.1/32 is directly connected, Ethernet0/3
C        172.18.0.4/30 is directly connected, Ethernet0/0
L        172.18.0.5/32 is directly connected, Ethernet0/0
C        172.18.0.8/30 is directly connected, Ethernet0/1
L        172.18.0.9/32 is directly connected, Ethernet0/1
```  

**Таблица маршрутизации IPv6 на R14**  
```
C   2001:DB8:ACAD:2::18:0/126 [0/0]
     via Ethernet0/3, directly connected
L   2001:DB8:ACAD:2::18:2/128 [0/0]
     via Ethernet0/3, receive
C   2001:DB8:ACAD:2::18:4/126 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:ACAD:2::18:5/128 [0/0]
     via Ethernet0/0, receive
C   2001:DB8:ACAD:2::18:8/126 [0/0]
     via Ethernet0/1, directly connected
L   2001:DB8:ACAD:2::18:9/128 [0/0]
     via Ethernet0/1, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурация маршрутизатора R15**  
```
Current configuration : 1174 bytes
!
! Last configuration change at 16:26:34 +07 Thu Apr 15 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R15
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R13-ET0/2
 ip address 172.18.0.17 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:12/126
!
interface Ethernet0/1
 description TO-R12-ET0/3
 ip address 172.18.0.13 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:19/126
!
interface Ethernet0/2
 no ip address
!
interface Ethernet0/3
 description TO-R20-ET0/0
 ip address 172.18.0.21 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:15/126
!
end
```  
**Таблица маршрутизации IPv4 на R15**  
```
Gateway of last resort is not set
      172.18.0.0/16 is variably subnetted, 6 subnets, 2 masks
C        172.18.0.12/30 is directly connected, Ethernet0/1
L        172.18.0.13/32 is directly connected, Ethernet0/1
C        172.18.0.16/30 is directly connected, Ethernet0/0
L        172.18.0.17/32 is directly connected, Ethernet0/0
C        172.18.0.20/30 is directly connected, Ethernet0/3
L        172.18.0.21/32 is directly connected, Ethernet0/3

```  

**Таблица маршрутизации IPv6 на R15**  
```
C   2001:DB8:ACAD:2::18:10/126 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:ACAD:2::18:12/128 [0/0]
     via Ethernet0/0, receive
C   2001:DB8:ACAD:2::18:14/126 [0/0]
     via Ethernet0/3, directly connected
L   2001:DB8:ACAD:2::18:15/128 [0/0]
     via Ethernet0/3, receive
C   2001:DB8:ACAD:2::18:18/126 [0/0]
     via Ethernet0/1, directly connected
L   2001:DB8:ACAD:2::18:19/128 [0/0]
     via Ethernet0/1, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурация маршрутизатора R19**  
```
Current configuration : 1092 bytes
!
! Last configuration change at 17:02:32 +07 Thu Apr 15 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R19
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R14-ET0/3
 ip address 172.18.0.2 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:1/126
!
interface Ethernet0/1
 no ip address
 shutdown
!
interface Ethernet0/2
 no ip address
 shutdown
!
interface Ethernet0/3
 no ip address
 shutdown
!
ip route 0.0.0.0 0.0.0.0 172.18.0.1
!
ipv6 route ::/0 2001:DB8:ACAD:2::18:2
!
end
```  

**Таблица маршрутизации IPv4 на R19**  
```
Gateway of last resort is 172.18.0.1 to network 0.0.0.0
S*    0.0.0.0/0 [1/0] via 172.18.0.1
      172.18.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.18.0.0/30 is directly connected, Ethernet0/0
L        172.18.0.2/32 is directly connected, Ethernet0/0
```  

**Таблица маршрутизации IPv6 на R19**  
```
S   ::/0 [1/0]
     via 2001:DB8:ACAD:2::18:2
C   2001:DB8:ACAD:2::18:0/126 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:ACAD:2::18:1/128 [0/0]
     via Ethernet0/0, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурация маршрутизатора R20**  
```
Current configuration : 1093 bytes
!
! Last configuration change at 17:05:56 +07 Thu Apr 15 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R20
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R15-ET0/3
 ip address 172.18.0.22 255.255.255.252
 ipv6 address 2001:DB8:ACAD:2::18:16/126
!
interface Ethernet0/1
 no ip address
 shutdown
!
interface Ethernet0/2
 no ip address
 shutdown
!
interface Ethernet0/3
 no ip address
 shutdown
!
ip route 0.0.0.0 0.0.0.0 172.18.0.21
!
ipv6 route ::/0 2001:DB8:ACAD:2::15
!
end
```  

**Таблица маршрутизации IPv4 на R20**  
```
Gateway of last resort is 172.18.0.21 to network 0.0.0.0
S*    0.0.0.0/0 [1/0] via 172.18.0.21
      172.18.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.18.0.20/30 is directly connected, Ethernet0/0
L        172.18.0.22/32 is directly connected, Ethernet0/0
```  

**Таблица маршрутизации IPv6 на R20**  
```
C   2001:DB8:ACAD:2::18:14/126 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:ACAD:2::18:16/128 [0/0]
     via Ethernet0/0, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурации рабочих станций**  
**VPC1**  
```
NAME   IP/MASK              GATEWAY                             GATEWAY
VPCS1  172.18.12.10/25      172.18.12.1
       fe80::250:79ff:fe66:6801/64
       2001:db8:acad:2::12:6/120
```  

**VPC7**  
```
NAME   IP/MASK              GATEWAY                             GATEWAY
VPCS1  172.18.14.10/25      172.18.14.1
       fe80::250:79ff:fe66:6807/64
       2001:db8:acad:2::14:6/120
```  

**Схема сети г. Санкт-Петербург**  
![](https://github.com/merkelev/neteng/blob/main/labs/4-IPv4-IPv6/NET-SP2.png)  

Добавил линк между R16 и R17.    

Для г. Санкт-Петербург использованы адреса IPv4 - 172.22.0.0/16 и IPv6 - 2001:DB8:ACAD:1::0/64 

**Таблица адресов IPv4 г. Санкт-Петербург**  
| Device   | Interface     | IP Address | Subnet Mask | Default Gateway | Description  | 
| -------- | ------------- | --------   | --------    | --------        | ------------ |
| R18      | Et0/0         | 172.22.18.1  | 255.255.255.252 |           | to R16:et0/1 |
|          | Et0/1         | 172.22.18.5  | 255.255.255.252 |           | to R17:et0/1 |
| R16      | Et0/0         |              |                 |           | to SW10:et0/3|
|          | Et0/0.22      | 172.22.22.2  | 255.255.255.128 |           |              |   
|          | Et0/0.24      | 172.22.24.2  | 255.255.255.128 |           |              |   
|          | Et0/0.99      | 172.22.99.2  | 255.255.255.128 |           |              |     
|          | Et0/1         | 172.22.18.2  | 255.255.255.252 |172.22.18.1| to R18:et0/0 |   
|          | Et0/2         | 172.22.18.9  | 255.255.255.252 |           | to R32:et0/0 |   
|          | Et0/3         | 172.22.18.13 | 255.255.255.252 |           | to R17:et0/3 |          
| R17      | Et0/0         |              |                 |           | to SW9:et0/3 | 
|          | Et0/0.22      | 172.22.22.3  | 255.255.255.128 |           |              |
|          | Et0/0.24      | 172.22.24.3  | 255.255.255.128 |           |              |   
|          | Et0/0.99      | 172.22.99.3  | 255.255.255.128 |           |              |     
|          | Et0/1         | 172.22.18.6  | 255.255.255.252 |172.22.18.5| to R18:et0/1 |
|          | Et0/3         | 172.22.18.14 | 255.255.255.252 |           | to R16:et0/3 |
| R32      | Et0/0         | 172.22.18.10 | 255.255.255.252 |           | to R16:et0/2 |
| SW9      | VLAN99        | 172.22.99.5  | 255.255.255.128 |           |              |
| SW10     | VLAN99        | 172.22.99.7  | 255.255.255.128 |           |              |
| VPC8     | NIC           | 172.22.22.6  | 255.255.255.128 |172.22.22.1|              |
| VPC      | NIC           | 172.22.24.6  | 255.255.255.128 |172.22.24.1|              |

**Таблица адресов IPv6 г. Санкт-Петербург**  
| Device   | Interface      | IPv6 Address          |Prefix| Default Gateway       | Description | 
| -------- | -------------  |  --------             | ---  | --------              | ------------|
| R18      | Et0/0          | 2001:DB8:ACAD:1::18:5 | /126 |                       |             |
|          | Et0/1          | 2001:DB8:ACAD:1::18:9 | /126 |                       |             |
| R16      | Et0/0          |                       |      |                       |             |       
|          | Et0/0.22       | 2001:DB8:ACAD:1::22:1 | /120 |                       |             |               
|          |                | 2001:DB8:ACAD:1::22:2 | /120 |                       |             |
|          | Et0/0.24       | 2001:DB8:ACAD:1::24:1 | /120 |                       |             |
|          |                | 2001:DB8:ACAD:1::24:2 | /120 |                       |             |
|          | Et0/0.99       | 2001:DB8:ACAD:1::99:1 | /120 |                       |             |
|          |                | 2001:DB8:ACAD:1::99:2 | /120 |                       |             |
|          | Et0/1          | 2001:DB8:ACAD:1::18:6 | /126 | 2001:DB8:ACAD:1::18:5 |             |
|          | Et0/2          | 2001:DB8:ACAD:1::18:1 | /126 |                       |             |
|          | Et0/3          | 2001:DB8:ACAD:1::18:D | /126 |                       |             |
| R17      | Et0/0          |                       |      |                       |             |
|          | Et0/0.22       | 2001:DB8:ACAD:1::22:1 | /120 |                       |             |
|          |                | 2001:DB8:ACAD:1::22:3 | /120 |                       |             |
|          | Et0/0.24       | 2001:DB8:ACAD:1::24:1 | /120 |                       |             |
|          |                | 2001:DB8:ACAD:1::24:3 | /120 |                       |             |
|          | Et0/0.99       | 2001:DB8:ACAD:1::99:1 | /120 |                       |             |
|          |                | 2001:DB8:ACAD:1::99:3 | /120 |                       |             |
|          | Et0/1          | 2001:DB8:ACAD:1::18:A | /126 | 2001:DB8:ACAD:1::18:9 |             |
|          | Et0/3          | 2001:DB8:ACAD:1::18:E | /126 |                       |             |
| R32      | Et0/0          | 2001:DB8:ACAD:1::18:2 | /126 |                       |             |
| SW9      | VLAN99         | 2001:DB8:ACAD:1::99:4 | /120 |                       |             |
| SW10     | VLAN99         | 2001:DB8:ACAD:1::99:5 | /120 |                       |             |
| VPC8     | NIC            | 2001:db8:acad:1::22:8 | /120 | 2001:db8:acad:1::22:1 |             |
| VPC      | NIC            | 2001:db8:acad:1::24:8 | /120 | 2001:db8:acad:1::24:1 |             |

**Таблица VLAN г. Санкт-Петербург**  
| VLAN | Name     | Interface Assigned         |
| ---- | -------  | -------------------------- |
| 77   | NATIVE   | N/A                        |
| 22   | MARKETING| SW9 Et0/2                  |
| 24   | MANAGERS | SW10 Et0/2                 |
| 54   | PRK-LOT  | SW10 Et0/1, Et1/1 - 3      |
|      |          | SW9 Et0/1, Et1/1 - 3       |
| 99   | MNGM-NET | SW9 VLAN99, SW10 VLAN99    |

**На маршрутизаторах R17 и R16 настроен HSRP для IPv4 и IPv6.**  
![](https://github.com/merkelev/neteng/blob/main/labs/4-IPv4-IPv6/HSRP-R16.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/4-IPv4-IPv6/HSRP-R17.png)  

**Конфигурация маршрутизатора R18**  
```
Current configuration : 1082 bytes
!
! Last configuration change at 14:52:33 +07 Wed Apr 14 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R18
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R16-ET0/1
 ip address 172.22.18.1 255.255.255.252
 ipv6 address 2001:DB8:ACAD:1::18:5/126
!
interface Ethernet0/1
 description TO-R17-ET0/1
 ip address 172.22.18.5 255.255.255.252
 ipv6 address 2001:DB8:ACAD:1::18:9/126
!
interface Ethernet0/2
 no ip address
 shutdown
!
interface Ethernet0/3
 no ip address
 shutdown
!
end
```  
**Таблица маршрутизации IPv4 на R18**  
```
Gateway of last resort is not set
      172.22.0.0/16 is variably subnetted, 4 subnets, 2 masks
C        172.22.18.0/30 is directly connected, Ethernet0/0
L        172.22.18.1/32 is directly connected, Ethernet0/0
C        172.22.18.4/30 is directly connected, Ethernet0/1
L        172.22.18.5/32 is directly connected, Ethernet0/1
```  
**Таблица маршрутизации IPv6 на R18**  
```
ND  ::/0 [2/0]
     via FE80::A8BB:CCFF:FE01:110, Ethernet0/0
C   2001:DB8:ACAD:1::18:4/126 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:ACAD:1::18:5/128 [0/0]
     via Ethernet0/0, receive
C   2001:DB8:ACAD:1::18:8/126 [0/0]
     via Ethernet0/1, directly connected
L   2001:DB8:ACAD:1::18:9/128 [0/0]
     via Ethernet0/1, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурация маршрутизатора R17**  
```
Current configuration : 2069 bytes
!
! Last configuration change at 15:02:44 +07 Wed Apr 14 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R17
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-SW9-ET0/3
 ip address 172.22.20.3 255.255.255.128
 standby version 2
 standby 20 ip 172.22.20.1
 standby 20 preempt
!
interface Ethernet0/0.22
 encapsulation dot1Q 22
 ip address 172.22.22.3 255.255.255.128
 standby version 2
 standby 22 ip 172.22.22.1
 standby 22 preempt
 standby 222 ipv6 2001:DB8:ACAD:1::22:1/120
 standby 222 preempt
 ipv6 address 2001:DB8:ACAD:1::22:3/120
!
interface Ethernet0/0.24
 encapsulation dot1Q 24
 ip address 172.22.24.3 255.255.255.128
 standby version 2
 standby 24 ip 172.22.24.1
 standby 24 preempt
 standby 224 ipv6 2001:DB8:ACAD:1::24:1/120
 standby 224 preempt
 ipv6 address 2001:DB8:ACAD:1::24:3/120
!
interface Ethernet0/0.99
 encapsulation dot1Q 99
 ip address 172.22.99.3 255.255.255.128
 standby version 2
 standby 99 ip 172.22.99.1
 standby 99 preempt
 standby 999 ipv6 2001:DB8:ACAD:1::99:1/120
 standby 999 preempt
 ipv6 address 2001:DB8:ACAD:1::99:3/120
!
interface Ethernet0/1
 description TO-R18-ET0/1
 ip address 172.22.18.6 255.255.255.252
 ipv6 address 2001:DB8:ACAD:1::18:A/126
!
interface Ethernet0/2
 no ip address
 shutdown
!
interface Ethernet0/3
 description TO-R16-ET0/3
 ip address 172.22.18.14 255.255.255.252
 ipv6 address 2001:DB8:ACAD:1::18:E/126
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 172.22.18.5
!
ipv6 route ::/0 2001:DB8:ACAD:1::18:9
!
end
```  
**Таблица маршутизации IPv4 на R17**  
```
Gateway of last resort is 172.22.18.5 to network 0.0.0.0
S*    0.0.0.0/0 [1/0] via 172.22.18.5
      172.22.0.0/16 is variably subnetted, 12 subnets, 3 masks
C        172.22.18.4/30 is directly connected, Ethernet0/1
L        172.22.18.6/32 is directly connected, Ethernet0/1
C        172.22.18.12/30 is directly connected, Ethernet0/3
L        172.22.18.14/32 is directly connected, Ethernet0/3
C        172.22.20.0/25 is directly connected, Ethernet0/0
L        172.22.20.3/32 is directly connected, Ethernet0/0
C        172.22.22.0/25 is directly connected, Ethernet0/0.22
L        172.22.22.3/32 is directly connected, Ethernet0/0.22
C        172.22.24.0/25 is directly connected, Ethernet0/0.24
L        172.22.24.3/32 is directly connected, Ethernet0/0.24
C        172.22.99.0/25 is directly connected, Ethernet0/0.99
L        172.22.99.3/32 is directly connected, Ethernet0/0.99
```  
**Таблица маршрутизации IPv6 на R17**  
```
S   ::/0 [1/0]
     via 2001:DB8:ACAD:1::18:9
C   2001:DB8:ACAD:1::18:8/126 [0/0]
     via Ethernet0/1, directly connected
L   2001:DB8:ACAD:1::18:A/128 [0/0]
     via Ethernet0/1, receive
C   2001:DB8:ACAD:1::18:C/126 [0/0]
     via Ethernet0/3, directly connected
L   2001:DB8:ACAD:1::18:E/128 [0/0]
     via Ethernet0/3, receive
C   2001:DB8:ACAD:1::22:0/120 [0/0]
     via Ethernet0/0.22, directly connected
L   2001:DB8:ACAD:1::22:3/128 [0/0]
     via Ethernet0/0.22, receive
C   2001:DB8:ACAD:1::24:0/120 [0/0]
     via Ethernet0/0.24, directly connected
L   2001:DB8:ACAD:1::24:3/128 [0/0]
     via Ethernet0/0.24, receive
C   2001:DB8:ACAD:1::99:0/120 [0/0]
     via Ethernet0/0.99, directly connected
L   2001:DB8:ACAD:1::99:3/128 [0/0]
     via Ethernet0/0.99, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурация маршрутизатора R16**  
```
Current configuration : 2090 bytes
!
! Last configuration change at 15:03:13 +07 Wed Apr 14 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R16
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-SW10-ET0/3
 no ip address
!
interface Ethernet0/0.22
 encapsulation dot1Q 22
 ip address 172.22.22.2 255.255.255.128
 standby version 2
 standby 22 ip 172.22.22.1
 standby 22 priority 120
 standby 222 ipv6 2001:DB8:ACAD:1::22:1/120
 standby 222 priority 120
 ipv6 address 2001:DB8:ACAD:1::22:2/120
!
interface Ethernet0/0.24
 encapsulation dot1Q 24
 ip address 172.22.24.2 255.255.255.128
 standby version 2
 standby 24 ip 172.22.24.1
 standby 24 priority 120
 standby 224 ipv6 2001:DB8:ACAD:1::24:1/120
 standby 224 priority 120
 ipv6 address 2001:DB8:ACAD:1::24:2/120
!
interface Ethernet0/0.99
 encapsulation dot1Q 99
 ip address 172.22.99.2 255.255.255.128
 standby version 2
 standby 99 ip 172.22.99.1
 standby 99 priority 120
 standby 999 ipv6 2001:DB8:ACAD:1::99:1/120
 standby 999 priority 120
 ipv6 address 2001:DB8:ACAD:1::99:2/120
!
interface Ethernet0/1
 description TO-R18-ET0/0
 ip address 172.22.18.2 255.255.255.252
 ipv6 address 2001:DB8:ACAD:1::18:6/126
!
interface Ethernet0/2
 description TO-R32-ET0/0
 ip address 172.22.18.9 255.255.255.252
 ipv6 address 2001:DB8:ACAD:1::18:1/126
!
interface Ethernet0/3
 description TO-R17-ET0/3
 ip address 172.22.18.13 255.255.255.252
 ipv6 address 2001:DB8:ACAD:1::18:D/126
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 172.22.18.1
!
ipv6 route ::/0 2001:DB8:ACAD:1::18:5
!
end
```  
**Таблица маршрутизации IPv4 на R16**  
```
Gateway of last resort is 172.22.18.1 to network 0.0.0.0
S*    0.0.0.0/0 [1/0] via 172.22.18.1
      172.22.0.0/16 is variably subnetted, 12 subnets, 3 masks
C        172.22.18.0/30 is directly connected, Ethernet0/1
L        172.22.18.2/32 is directly connected, Ethernet0/1
C        172.22.18.8/30 is directly connected, Ethernet0/2
L        172.22.18.9/32 is directly connected, Ethernet0/2
C        172.22.18.12/30 is directly connected, Ethernet0/3
L        172.22.18.13/32 is directly connected, Ethernet0/3
C        172.22.22.0/25 is directly connected, Ethernet0/0.22
L        172.22.22.2/32 is directly connected, Ethernet0/0.22
C        172.22.24.0/25 is directly connected, Ethernet0/0.24
L        172.22.24.2/32 is directly connected, Ethernet0/0.24
C        172.22.99.0/25 is directly connected, Ethernet0/0.99
L        172.22.99.2/32 is directly connected, Ethernet0/0.99
```  
**Таблица маршрутизации IPv6 на R16**  
```
S   ::/0 [1/0]
     via 2001:DB8:ACAD:1::18:5
C   2001:DB8:ACAD:1::18:0/126 [0/0]
     via Ethernet0/2, directly connected
L   2001:DB8:ACAD:1::18:1/128 [0/0]
     via Ethernet0/2, receive
C   2001:DB8:ACAD:1::18:4/126 [0/0]
     via Ethernet0/1, directly connected
L   2001:DB8:ACAD:1::18:6/128 [0/0]
     via Ethernet0/1, receive
C   2001:DB8:ACAD:1::18:C/126 [0/0]
     via Ethernet0/3, directly connected
L   2001:DB8:ACAD:1::18:D/128 [0/0]
     via Ethernet0/3, receive
C   2001:DB8:ACAD:1::22:0/120 [0/0]
     via Ethernet0/0.22, directly connected
L   2001:DB8:ACAD:1::22:1/128 [0/0]
     via Ethernet0/0.22, receive
L   2001:DB8:ACAD:1::22:2/128 [0/0]
     via Ethernet0/0.22, receive
C   2001:DB8:ACAD:1::24:0/120 [0/0]
     via Ethernet0/0.24, directly connected
L   2001:DB8:ACAD:1::24:1/128 [0/0]
     via Ethernet0/0.24, receive
L   2001:DB8:ACAD:1::24:2/128 [0/0]
     via Ethernet0/0.24, receive
C   2001:DB8:ACAD:1::99:0/120 [0/0]
     via Ethernet0/0.99, directly connected
L   2001:DB8:ACAD:1::99:1/128 [0/0]
     via Ethernet0/0.99, receive
L   2001:DB8:ACAD:1::99:2/128 [0/0]
     via Ethernet0/0.99, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**R32**  
```
Current configuration : 1003 bytes
!
! Last configuration change at 14:41:20 +07 Wed Apr 14 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R32
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
no ip domain lookup
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R16-ET0/2
 ip address 192.168.18.10 255.255.255.252
 ipv6 address 2001:DB8:ACAD:1::18:2/126
!
interface Ethernet0/1
 no ip address
 shutdown
!
interface Ethernet0/2
 no ip address
 shutdown
!
interface Ethernet0/3
 no ip address
 shutdown
!
ip forward-protocol nd
!
end
```  
**Таблица маршрутизации IPv4 на R32**  
```
Gateway of last resort is not set
      192.168.18.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.18.8/30 is directly connected, Ethernet0/0
L        192.168.18.10/32 is directly connected, Ethernet0/0
```  
**Таблица маршрутизации IPv6 на R32**  
```
ND  ::/0 [2/0]
     via FE80::A8BB:CCFF:FE01:120, Ethernet0/0
C   2001:DB8:ACAD:1::18:0/126 [0/0]
     via Ethernet0/0, directly connected
L   2001:DB8:ACAD:1::18:2/128 [0/0]
     via Ethernet0/0, receive
L   FF00::/8 [0/0]
     via Null0, receive
```  

**Конфигурация коммутатора SW9**  
```
Current configuration : 1733 bytes
!
! Last configuration change at 13:58:39 +07 Wed Apr 14 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW9
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
ip cef
ipv6 unicast-routing
ipv6 cef
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
interface Port-channel1
 switchport trunk allowed vlan 1,22,24,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/0
 switchport trunk allowed vlan 1,22,24,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
!
interface Ethernet0/1
 switchport trunk allowed vlan 1,22,24,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
!
interface Ethernet0/2
 switchport access vlan 22
 switchport mode access
!
interface Ethernet0/3
 switchport trunk allowed vlan 1,22,24,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/0
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan99
 ip address 172.22.99.5 255.255.255.128
 ipv6 address 2001:DB8:ACAD:1::99:4/120
!
end
```  
**Конфигураци коммутатора SW10**  
```
Current configuration : 1734 bytes
!
! Last configuration change at 13:56:15 +07 Wed Apr 14 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW10
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
ip cef
ipv6 unicast-routing
ipv6 cef
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
interface Port-channel1
 switchport trunk allowed vlan 1,22,24,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/0
 switchport trunk allowed vlan 1,22,24,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
!
interface Ethernet0/1
 switchport trunk allowed vlan 1,22,24,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode active
!
interface Ethernet0/2
 switchport access vlan 24
 switchport mode access
!
interface Ethernet0/3
 switchport trunk allowed vlan 1,22,24,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/0
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan99
 ip address 172.22.99.7 255.255.255.128
 ipv6 address 2001:DB8:ACAD:1::99:5/120
!
end
```  

**Конфигурации рабочих станций**  
**VPC8**  
```
NAME   IP/MASK              GATEWAY                             GATEWAY
VPCS1  172.22.22.6/25       172.22.22.1
       fe80::250:79ff:fe66:6808/64
       2001:db8:acad:1::22:8/120
```  

**VPC**  
```
NAME   IP/MASK              GATEWAY                             GATEWAY
VPCS1  172.22.24.6/25       172.22.24.1
       fe80::250:79ff:fe66:680b/64
       2001:db8:acad:1::24:8/120
```

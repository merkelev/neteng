### Настройка DHCPv4

**Цель:**  
**Настроить DCHPv4 на маршрутизаторе Cisco**  

![Топология сети в программе EVE-NG](https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/3-DHCPv4.png)

**Таблица адресов IPv4**  
| Device | Interface | IP Address | Subnet Mask | Default Gateway | 
| -------- | ------------- | -------- | -------- | -------- |
| R1 | Gi0/0 | 10.0.0.1 | 255.255.255.252 | N/A |
|    | Gi0/1 | N/A | N/A | N/A | 
|    | Gi0/1.100 | 192.168.1.1 | 255.255.255.192 | N/A |
|    | Gi0/1.200 | 192.168.1.65 | 255.255.255.224 | N/A |
|    | Gi0/1.1000 | N/A | N/A | N/A |
| R2 | Gi0/0 | 10.0.0.2 | 255.255.255.252 | N/A |
|    | Gi0/1 | 192.168.1.97 | 255.255.255.240 | N/A |
| SW1 | VLAN 200 | 192.168.1.66 | 255.255.255.224 | 192.168.1.65 |
| SW2 | VLAN 1   | N/A | N/A | N/A |
| VPC5 | NIC | DHCP | DHCP | DHCP |
| VPC6 | NIC | DHCP | DHCP | DHCP |

**Таблица VLAN**  
| VLAN | Name | Interface Assigned |
| ---- | ---- | ------------------ |
| 1    | N/A  | SW2: Gi0/3         |
| 100  | CLIENTS | SW1: Gi0/2      |
| 200  | MNGM | SW1: VLAN 200      |
| 999  | PRK_LOT | SW1: Gi0/0, Gi0/3, Gi1/0 - 3 |
| 1000 | Native | N/A              |

**Выполнение:**  
**Часть 1: Распредиление адресов, базовая настройка маршрутизаторов и коммутаторов.**  
Согласно заданию сеть 192.168.1.0/24 разделил на подсети.    
Сеть А, для VLAN 100 CLIENTS: 192.168.1.0/26  
Network: 192.168.1.0  
Mask: 255.255.255.192 или 26 бита  
Broadcast: 192.168.1.63  
Hosts: 62  

Сеть B, для VLAN 200 MNGM: 192.168.1.64/27  
Network: 192.168.1.64  
Mask: 255.255.255.224 или 27 бит  
Broadcast: 192.168.1.95  
Hosts: 30  

Сеть C, для VLAN 1 CLIENTS: 192.168.1.96/28  
Network: 192.168.1.96  
Mask: 255.255.255.240 или 28 бит  
Broadcast: 192.168.1.111  
Hosts: 14  

На маршрутизаторе R1 на интерфейсе Gi0/1 настроил VLAN 100,200,1000 и настроил IP адреса:  
```
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 media-type rj45
!
interface GigabitEthernet0/1.100
 description CLIENTS
 encapsulation dot1Q 100
 ip address 192.168.1.1 255.255.255.192
!
interface GigabitEthernet0/1.200
 description MNGM
 encapsulation dot1Q 200
 ip address 192.168.1.65 255.255.255.224
!
interface GigabitEthernet0/1.1000
 encapsulation dot1Q 1000
!
```
Настроил на маршрутизаторе R1 IP адрес на интерфейсе Gi0/0:  
```
interface GigabitEthernet0/0
 ip address 10.0.0.1 255.255.255.252
 duplex auto
 speed auto
 media-type rj45
```

На коммутаторе SW1 настроил транковый порт на Gi0/1:  
```
interface GigabitEthernet0/1
 switchport trunk allowed vlan 100,200,1000
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 1000
 switchport mode trunk
 media-type rj45
 negotiation auto
```

Настроил VLAN 200 и назначил IP адрес:  
```
interface Vlan200
 ip address 192.168.1.66 255.255.255.224
```
И указал шлюз:  
```
ip default-gateway 192.168.1.65
```

Настроил VLAN 100,200 и 999:  
```
vlan 100
 name CLIENTS
vlan 200
 name MNGM
vlan 999
 name PRK_LOT
```  

```
SW1#sh vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active
100  CLIENTS                          active    Gi0/2
200  MNGM                             active
999  PRK_LOT                          active    Gi0/0, Gi0/3, Gi1/0, Gi1/1
                                                Gi1/2, Gi1/3
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup
```
Настроил интерфейсы Gi0/0, Gi0/3, Gi1/0 - 3 и отключил их:  
```
interface GigabitEthernet0/0
 switchport access vlan 999
 switchport trunk encapsulation dot1q
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet0/3
 switchport access vlan 999
 switchport trunk encapsulation dot1q
 switchport mode access
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet1/0
 switchport access vlan 999
 switchport trunk encapsulation dot1q
 switchport mode access
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet1/1
 switchport access vlan 999
 switchport trunk encapsulation dot1q
 switchport mode access
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet1/2
 switchport access vlan 999
 switchport trunk encapsulation dot1q
 switchport mode access
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet1/3
 switchport access vlan 999
 switchport trunk encapsulation dot1q
 switchport mode access
 shutdown
 media-type rj45
 negotiation auto
!
```

Настроил интерфейс Gi0/2 как порт доступа для ПК VPC5:  
```
interface GigabitEthernet0/2
 switchport access vlan 100
 switchport trunk encapsulation dot1q
 switchport mode access
 media-type rj45
 negotiation auto
```

На маршрутизаторе R2 настроил интерфейсы Gi0/0 и Gi0/1:  
```
interface GigabitEthernet0/0
 ip address 10.0.0.2 255.255.255.252
 duplex auto
 speed auto
 media-type rj45
!
interface GigabitEthernet0/1
 description CLIENTS
 ip address 192.168.1.97 255.255.255.240
 duplex auto
 speed auto
 media-type rj45
 ```
 
 На коммутаторе SW2 выключил все неиспользуемые порты:  
```
interface GigabitEthernet0/0
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet0/2
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet1/0
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet1/1
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet1/2
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet1/3
 shutdown
 media-type rj45
 negotiation auto
!
```

**Часть 2: Настройка DHCPv4 на маршрутизаторе R1.**  
Настроил DHCP на маршрутизаторе R1:  
```
ip dhcp pool POOL-CLIENTS
 network 192.168.1.0 255.255.255.192
 domain-name ccna-lab.com
 default-router 192.168.1.1
 lease 2 12 30 
!
ip dhcp pool R2_CLIENT_LAN
 network 192.168.1.96 255.255.255.240
 domain-name ccna-lab.com
 default-router 192.168.1.97
 lease 2 12 30
! 
``` 

На ПК VPC5 включил получение адреса по DHCP и выполнил пинг до шлюза:  
```
VPCS> ip dhcp
DDORA IP 192.168.1.2/26 GW 192.168.1.1

VPCS> ping 192.168.1.1

84 bytes from 192.168.1.1 icmp_seq=1 ttl=255 time=5.008 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=255 time=6.268 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=255 time=5.664 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=255 time=4.491 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=255 time=6.580 ms
```

**Часть 3: Настройка DHCP Relay на маршрутизаторе R2.**  
Настроил relay на машрутизаторе R2:  
```
interface GigabitEthernet0/1
 description CLIENTS
 ip address 192.168.1.97 255.255.255.240
 ip helper-address 10.0.0.1
 duplex auto
 speed auto
 media-type rj45
```

На ПК VPC6 включил получение адреса по DHCP и выполнил пинг до адреса 192.168.1.1:  
```
VPCS> ip dhcp
DORA IP 192.168.1.98/28 GW 192.168.1.97

VPCS> ping 192.168.1.1

84 bytes from 192.168.1.1 icmp_seq=1 ttl=254 time=17.153 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=254 time=15.468 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=254 time=7.624 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=254 time=14.826 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=254 time=7.397 ms
```

**Вывод команды show ip dhcp binding на маршрутизаторе R1:**  
```
R1#sh ip dhcp binding
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
192.168.1.2         0100.5079.6668.05       Mar 22 2021 05:25 PM    Automatic
192.168.1.98        0100.5079.6668.06       Mar 22 2021 05:41 PM    Automatic
```

**Вывод команды show ip dhcp server statistics на маршрутизаторе R1:**  
```
R1#sh ip dhcp server statistics
Memory usage         42140
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         21
DHCPREQUEST          7
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            7
DHCPACK              7
DHCPNAK              0
```

**Вывод команды show ip dhcp server statistics на маршрутизаторе R2:**  
```
R2#sh ip dhcp server statistics
Memory usage         30757
Address pools        0
Database agents      0
Automatic bindings   0
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         0
DHCPREQUEST          0
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            0
DHCPACK              0
DHCPNAK              0
```

**Конфиг маршрутизатора R1:**  
(https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/Config-R1.txt)  

**Конфиг маршрутизатора R2:**  
(https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/Config-R2.txt)  

**Конфиг коммутатора SW1:**  
(https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/Config-SW1.txt)  

**Конфиг коммутатора SW2:**  
(https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/Config-SW2.txt)  


### Настройка DHCPv6
**Цель: Настроить DHCPv6 на маршрутизаторе Cisco**  

![Топология сети в программе EVE-NG](https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/3-DHCPv6.png)  

**Таблица адресов IPv6**  
| Device | Interface | IPv6 Address |
| ------ | --------- | ------------ |
| R7     | Gi0/0     | 2001:db8:acad:2::1/64 |
|        |           | fe80::1 |
|        | Gi0/1     | 2001:db8:acad:1::1/64 |
|        |           | fe80::1 |
| R8     | Gi0/0     | 2001:db8:acad:2::2/64 |
|        |           | fe80::2 |
|        | Gi0/1     | 2001:db8:acad:3::1/64 |
|        |           | fe80::1 |
| Win7-1 | NIC       | DHCP    |
| Win7-2 | NIC       | DHCP    |

**Часть 1: Базовая настройка маршрутизаторов и коммутаторов.**  
Отключил все неиспользуемые порты на SW9:  
```
interface GigabitEthernet0/2
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet0/3
 shutdown
 media-type rj45
 negotiation auto
!
```

Отключил все неиспользуемые порты на SW10:  
```
interface GigabitEthernet0/2
 shutdown
 media-type rj45
 negotiation auto
!
interface GigabitEthernet0/3
 shutdown
 media-type rj45
 negotiation auto
!
```

Настроил интерфейсы Gi0/0 и Gi0/1 на маршрутизаторе R7 и настроил шлюз по умолчанию:  
```
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 media-type rj45
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:2::1/64
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 media-type rj45
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
!
ipv6 route ::/0 2001:DB8:ACAD:2::2
```

Настроил интерфейсы Gi0/0 и Gi0/1 на маршрутизаторе R8 и настроил шлюз по умолчанию:  
```
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 media-type rj45
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:ACAD:2::2/64
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 media-type rj45
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:3::1/64
!
ipv6 route ::/0 2001:DB8:ACAD:2::1
```

Проверил с R7 доступность маршрутизатора R8:
```
R7#ping ipv6 2001:DB8:ACAD:2::2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:2::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/5/9 ms
```

**Часть 2: Проверка назначения адреса SLAAC из R7.**  
На ПК Win7-1:  
![](https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/Path2.png)  

Часть хоста сгенерированна eui-64

**Часть 3: Настройка и проверка сервера DHCPv6 на R7.**  
Настроил stateless DHCPv6 на маршрутизаторе R7:  
```
ipv6 dhcp pool R7-STATELESS
 dns-server 2001:DB8:ACAD::254
 domain-name STATELESS.COM
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 media-type rj45
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
 ipv6 nd other-config-flag
 ipv6 dhcp server R7-STATELESS
 ```

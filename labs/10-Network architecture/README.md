### Network architecture

**Цель: Задокументировать и настроить адресное пространство сети в г. Москва и г. Санкт-Петербург**  

Добавил линки между R12 и SW5, R13 и SW4, SW3 и SW2. Линки добавил для избыточности когда будет динамическая маршрутизация в сети.    
Удалил один линк между SW4 и SW5. Заколцевал сеть между коммутаторами на L2.  

**Таблица адресов IPv4 г. Москва**  
| Device   | Interface     | IP Address | Subnet Mask | Default Gateway | 
| -------- | ------------- | --------   | --------    | --------        |
| VPC1     | NIC           | 172.17.12.2 | 255.255.255.248 | 172.17.12.1|
| VPC7     | NIC           | 172.17.14.3 | 255.255.255.248 | 172.17.14.1|
| SW4      | VLAN9         | 172.17.9.10 | 255.255.255.240 | 172.17.9.1 |
|          | VLAN12        | 172.17.12.1 | 255.255.255.248 |            |
|          | Et1/0         | 172.16.0.6  | 255.255.255.252 | 172.16.0.5 |
|          | Et0/2         | 172.16.0.10 | 255.255.255.252 |            |
| SW5      | VLAN9         | 172.17.9.11 | 255.255.255.240 | 172.17.9.1 |
|          | VLAN14        | 172.17.14.1 | 255.255.255.248 |            |
|          | Et1/0         | 172.16.0.2  | 255.255.255.252 | 172.16.0.1 |
|          | Et0/2         | 172.16.0.14 | 255.255.255.252 |            |
| SW3      | VLAN9         | 172.17.9.12 | 255.255.255.240 | 172.17.9.1 |
| SW2      | VLAN9         | 172.17.9.13 | 255.255.255.240 | 172.17.9.1 |
| R12      | Et0/0         | 172.16.0.5  | 255.255.255.252 |            |
|          | Et0/1         | 172.16.0.13 | 255.255.255.252 |            |
|          | Et0/3         | 172.18.0.9  | 255.255.255.252 |            |
|          | Et0/2         | 172.18.0.13 | 255.255.255.252 |            |
| R13      | Et0/0         | 172.16.0.1  | 255.255.255.252 |            |
|          | Et0/1         | 172.16.0.9  | 255.255.255.252 |            |
|          | Et0/3         | 172.18.0.5  | 255.255.255.252 |            |
|          | Et0/2         | 172.18.0.2  | 255.255.255.252 |            |
| R15      | Et0/0         | 172.18.0.1  | 255.255.255.252 |            |
|          | Et0/1         | 172.18.0.10 | 255.255.255.252 |            |
|          | Et0/3         | 172.19.0.5  | 255.255.255.252 |            |
| R14      | Et0/1         | 172.18.0.6  | 255.255.255.252 |            |
|          | Et0/0         | 172.18.0.14 | 255.255.255.252 |            |
|          | Et0/3         | 172.19.0.1  | 255.255.255.252 |            |
| R19      | Et0/0         | 172.19.0.2  | 255.255.255.252 | 172.19.0.1 |
| R20      | Et0/0         | 172.19.0.6  | 255.255.255.252 | 172.19.0.5 |

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

**Конфигурационные файлы коммутаторов:**  
**SW3**  
```
Current configuration : 1755 bytes
!
! Last configuration change at 16:07:20 +07 Sat Apr 10 2021
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
!
!
no aaa new-model
clock timezone +07 7 0
!
!
!
!
!
vtp mode transparent
!
!
!
ip cef
no ipv6 cef
!
!
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
!
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
 duplex auto
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
 ip address 172.17.9.12 255.255.255.240
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
!
!
!
!
!
!
control-plane
!
!
line con 0
 logging synchronous
line aux 0
line vty 0 4
 login
!
!
end
```

**Таблица адресов IPv4 г. Санкт-Петербург**  
| Device   | Interface     | IP Address | Subnet Mask | Default Gateway | 
| -------- | ------------- | --------   | --------    | --------        |
| VPC1     | NIC           | 172.17.12.2 | 255.255.255.248 | 172.17.12.1|
| VPC7     | NIC           | 172.17.14.3 | 255.255.255.248 | 172.17.14.1|
| SW4      | VLAN9         | 172.17.9.10 | 255.255.255.240 | 172.17.9.1 |
|          | VLAN12        | 172.17.12.1 | 255.255.255.248 |            |
|          | Et1/0         | 172.16.0.6  | 255.255.255.252 | 172.16.0.5 |
|          | Et0/2         | 172.16.0.10 | 255.255.255.252 |            |
| SW5      | VLAN9         | 172.17.9.11 | 255.255.255.240 | 172.17.9.1 |
|          | VLAN14        | 172.17.14.1 | 255.255.255.248 |            |
|          | Et1/0         | 172.16.0.2  | 255.255.255.252 | 172.16.0.1 |
|          | Et0/2         | 172.16.0.14 | 255.255.255.252 |            |
| SW3      | VLAN9         | 172.17.9.12 | 255.255.255.240 | 172.17.9.1 |
| SW2      | VLAN9         | 172.17.9.13 | 255.255.255.240 | 172.17.9.1 |
| R16      | Et0/1         | 192.168.18.2 | 255.255.255.252 |           |
|          | Et0/2         | 192.168.17.6 | 255.255.255.252 |           |
| R17      | Et0/2         | 192.168.17.2 | 255.255.255.252 |           |
|          | Et0/1         | 192.168.18.6 | 255.255.255.252 |           |
| R18      | Et0/0         | 192.168.18.1 | 255.255.255.252 |           |
|          | Et0/1         | 192.168.18.5 | 255.255.255.252 |           |    
| R32      | Et0/1         | 192.168.17.1 | 255.255.255.252 |           |
|          | Et0/0         | 192.168.17.5 | 255.255.255.252 |           |

**Таблица VLAN г. Санкт-Петербург**  
| VLAN | Name | Interface Assigned  |
| ---- | ----- | ------------------ |
| 77   | NATIVE| N/A                |
| 9    | MNGM  | SW4 VLAN9, SW5 VLAN9, SW3 VLAN9, SW2 VLAN9|
| 12   |    |                    |
| 14   |  |                    |
| 54   | PRK-LOT |                  |


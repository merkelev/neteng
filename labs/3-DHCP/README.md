### Настройка DHCPv4

**Цель:**  
**Настроить DCHPv4 на маршрутизаторе Cisco**  

![Топология сети в программе EVE-NG](https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/3-DHCPv4.png)

**Таблица адресов IPv4**  
| Device | Interface | IP Address | Subnet Mask | Default Gateway | 
| -------- | ------------- | -------- | -------- | -------- |
| R1 | Gi0/0 | 10.0.0.1 | 255.255.255.252 | N/A |
|    | Gi0/1 | N/A | N/A |    | 
|    | Gi0/1.100 | 192.168.1.1 | 255.255.255.192 | N/A |
|    | Gi0/1.200 | 192.168.1.65 | 255.255.255.224 | N/A |
|    | Gi0/1.1000 | N/A | N/A |   |
| R2 | Gi0/0 | 10.0.0.2 | 255.255.255.252 | N/A |
|    | Gi0/1 | 192.168.1.97 | 255.255.255.240 | N/A |
| SW1 | VLAN 200 | 192.168.1.66 | 255.255.255.224 | 192.168.1.65 |
| SW2 | VLAN 1   | 192.168.1.98 | 255.255.255.240 | 192.168.1.97 |
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
**Часть 1:**  
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


### Настройка DHCPv4

**Цель:**  
**Настроить DCHPv4 на маршрутизаторе Cisco**  

![Топология сети в программе EVE-NG](https://github.com/merkelev/neteng/blob/main/labs/3-DHCP/3-DHCPv4.png)

**Таблица адресов IPv4**  
| Device | Interface | IP Address | Subnet Mask | Default Gateway | 
| -------- | ------------- | -------- | -------- | -------- |
| R1 | Gi0/0 | 10.0.0.1 | 255.255.255.252 | N/A |
|    | Gi0/1 | N/A | N/A |    | 
|    | Gi0/1.100 |    |     |   |
|    | Gi0/1.200 |    |     |   |
|    | Gi0/1.1000 | N/A | N/A |   |
| R2 | Gi0/0 | 10.0.0.2 | 255.255.255.252 | N/A |
|    | Gi0/1 |          |                 |     |
| SW1 | VLAN 200 |       |                 |     |
| SW2 | VLAN 1   |       |                 |     |
| VPC5 | NIC | DHCP | DHCP | DHCP |
| VPC6 | NIC | DHCP | DHCP | DHCP |

**Таблица VLAN**  
| VLAN | Name | Interface Assigned |
| ---- | ---- | ------------------ |
| 1    | N/A  | SW2: Gi0/3         |
| 100  | Clients | SW1: Gi0/2      |
| 200  | MNGM | SW1: VLAN 200      |
| 999  | PRK_LOT | SW1: Gi0/0, Gi0/3, Gi1/0 - 3 |
| 1000 | Native | N/A              |


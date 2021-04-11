**PBR**  

**Схема сети в Чокурдах**  
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/NET-CHOKURDAH.png)

**Таблица адресов IPv4 Чокурдах**  
| Device   | Interface     | IP Address | Subnet Mask     | Default Gateway | 
| -------- | ------------- | --------   | --------        | --------        |
| VPC30    | NIC           | 172.20.1.2 | 255.255.255.248 | 172.20.1.1      |
| VPC31    | NIC           | 172.20.2.2 | 255.255.255.248 | 172.20.2.1      |
| SW29     | VLAN99        | 172.20.9.4 | 255.255.255.248 | 172.20.9.1      |    
| R28      | Et0/1         |            |                 |                 |
|          | Et0/0         |            |                 |                 |
|          | Et0/2.22      | 172.20.1.1 | 255.255.255.248 |                 |
|          | Et0/2.24      | 172.20.2.1 | 255.255.255.248 |                 |
|          | Et0/2.99      | 172.20.9.1 | 255.255.255.248 |                 |

**Таблица VLAN Чокурдах**  
| VLAN | Name     | Interface Assigned |
| ---- | -----    | ------------------ |
| 77   | NATIVE   | N/A                |
| 99   | MNGM     | SW29: VLAN 99      |
| 22   | MARKETING| SW29: Et0/0        |
| 24   | MANAGERS | SW29: Et0/1        |
| 54   | PRK-LOT  |                    |

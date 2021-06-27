## VPN. GRE. DMVPN

**Схема сети**  
![](https://github.com/merkelev/neteng/blob/main/labs/13-VPN-GRE-DMVPN/images/2021-06-27_15-47-13.png)  

**Цель:**  
**1. Настроить GRE между офисами Москва и С.-Петербург**  
**2. Настроить DMVPN между офисами Москва и Чокурдах, Лабытнанги**  

**Выполнение**  

**Таблица адресов GRE туннелей**  
| Device   | Address        | Mask | Interface  |
| -------- | ---------      | ---- | ---------  |
| R15      | 109.226.218.80 | /32  | Loopback80 |
|          | 10.100.0.1     | /30  | Tunnel100  |
| R14      | 109.226.218.80 | /32  | Loopback80 |
|          | 10.100.0.1     | /30  | Tunnel100  |
| R18      | 75.100.20.80   | /32  | Loopback80 |
|          | 10.100.0.2     | /30  | Tunnel100  | 

**Таблица адресов DMVPN**  
| Device   | Address        | Mask | Interface  |
| -------- | ---------      | ---- | ---------  |
| R15      | 109.226.218.6  | /30  | Et0/1      |
|          | 10.200.0.1     | /24  | Tunnel100  |
| R14      | 109.226.218.5  | /30  | Et0/1      |
|          | 10.120.0.1     | /24  | Tunnel20   |
| R28      | 95.188.50.2    | /30  | Et0/0      |
|          | 10.200.0.3     | /24  | Tunnel10   |
|          | 95.188.100.2   | /30  | Et0/1      |
|          | 10.120.0.2     | /24  | Tunnel20   |
| R27      | 95.188.120.2   | /30  | Et0/0      |
|          | 10.200.0.2     | /24  | Tunnel10   |
|          | 10.120.0.3     | /24  | Tunnel20   |

**1. Настроить GRE между офисами Москва и С.-Петербург**  
Настройки GRE на R14 & R15  
R14  
```
interface Loopback80
 ip address 109.226.218.80 255.255.255.255
```  

```
interface Tunnel100
 description GRE-TUNNEL-TO-SP
 ip address 10.100.0.1 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip ospf 10 area 0
 tunnel source 109.226.218.80
 tunnel destination 75.100.20.80
```  

R15  
```
interface Loopback80
 ip address 109.226.218.80 255.255.255.255
```  

```
interface Tunnel100
 description GRE-TUNNEL-TO-SP
 ip address 10.100.0.1 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip ospf 10 area 0
 tunnel source 109.226.218.80
 tunnel destination 75.100.20.80
```  

Настройка GRE на R18  
```
interface Loopback80
 ip address 75.100.20.80 255.255.255.255
```  

```
interface Tunnel100
 description GRE-TUNNEL-TO-MSK
 ip address 10.100.0.2 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 ip ospf 10 area 0
 tunnel source 75.100.20.80
 tunnel destination 109.226.218.80
```  

Проверяюсостояние туннеля и пинг между узлами  
![](https://github.com/merkelev/neteng/blob/main/labs/13-VPN-GRE-DMVPN/images/GRE-R15-R18.png)  

Туннель работает. В случает отказа R15, туннель перестроится на R14.  

**2. Настроить DMVPN между офисами Москва и Чокурдах, Лабытнанги**  
Настройка DMVPN на R15 (Москва)  
```
interface Tunnel10
description DMVPN-HUB
ip address 10.200.0.1 255.255.255.0
no ip redirects
ip mtu 1400
ip nhrp map multicast dynamic
ip nhrp network-id 100
ip tcp adjust-mss 1360
ip ospf network broadcast
ip ospf priority 120
ip ospf 10 area 0
tunnel source 109.226.218.6
tunnel mode gre multipoint
```  

Настройка DMVPN на R14 (Москва)  
```
interface Tunnel20
description DMVPN-HUB
ip address 10.120.0.1 255.255.255.0
no ip redirects
ip mtu 1400
ip nhrp map multicast dynamic
ip nhrp network-id 200
ip tcp adjust-mss 1360
ip ospf network broadcast
ip ospf priority 10
ip ospf 10 area 0
keepalive 5 2
tunnel source 109.226.218.5
tunnel mode gre multipoint
```  
 
Настройка DMVPN на R28 (Чокурдах)  

```
interface Tunnel10
description DMVPN-SPOKE-R15
ip address 10.200.0.3 255.255.255.0
no ip redirects
ip mtu 1400
ip nhrp map multicast 109.226.218.6
ip nhrp map 10.200.0.1 109.226.218.6
ip nhrp network-id 100
ip nhrp nhs 10.200.0.1
ip tcp adjust-mss 1360
ip ospf network broadcast
ip ospf priority 20
ip ospf 10 area 0
tunnel source 95.188.50.2
tunnel mode gre multipoint
!
interface Tunnel20
description DMVPN-SPOKE-R15
ip address 10.120.0.2 255.255.255.0
no ip redirects
ip mtu 1400
ip nhrp map multicast 109.226.218.5
ip nhrp map 10.120.0.1 109.226.218.5
ip nhrp network-id 200
ip nhrp nhs 10.120.0.1
ip tcp adjust-mss 1360
ip ospf network broadcast
ip ospf priority 10
ip ospf 10 area 0
tunnel source 95.188.100.2
tunnel mode gre multipoint
!
```  
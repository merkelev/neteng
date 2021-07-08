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
| R15      | 109.226.218.100| /30  | Loopback100|
|          | 10.200.0.1     | /24  | Tunnel100  |
| R14      | 95.188.0.2     | /30  | Et0/1      |
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
 description FOR-GRE-TUNNEL-TO-R18
 ip address 109.226.218.80 255.255.255.255
```  

```
interface Tunnel100
 description GRE-TUNNEL-TO-R18-SP
 ip address 10.100.0.1 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 109.226.218.80
 tunnel destination 75.100.20.80
```  

R15  
```
interface Loopback80
 description GRE-TUNNEL-TO-R18-SP
 ip address 109.226.218.80 255.255.255.255
```  

```
interface Tunnel100
 description GRE-TUNNEL-TO-R18-SP
 ip address 10.100.0.1 255.255.255.252
 no ip redirects
 ip mtu 1400
 ip tcp adjust-mss 1360
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
 no ip redirects
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 75.100.20.80
 tunnel destination 109.226.218.80
```  

Проверяю состояние туннеля и пинг между узлами  
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
 no ip next-hop-self eigrp 4
 no ip split-horizon eigrp 4
 ip nhrp map multicast dynamic
 ip nhrp network-id 100
 ip tcp adjust-mss 1360
 tunnel source 109.226.218.100
 tunnel mode gre multipoint
 tunnel key 200
```  

Настройка DMVPN на R14 (Москва)  
```
interface Tunnel20
 description DMVPN-HUB
 ip address 10.120.0.1 255.255.255.0
 no ip redirects
 ip mtu 1400
 no ip next-hop-self eigrp 4
 no ip split-horizon eigrp 4
 ip nhrp map multicast dynamic
 ip nhrp network-id 200
 ip tcp adjust-mss 1360
 tunnel source 95.188.0.2
 tunnel mode gre multipoint
 tunnel key 120
```  
 
Настройка DMVPN на R28 (Чокурдах)  

```
interface Tunnel10
 description DMVPN-SPOKE-R15
 ip address 10.200.0.3 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip nhrp map multicast 109.226.218.100
 ip nhrp map 10.200.0.1 109.226.218.100
 ip nhrp network-id 100
 ip nhrp holdtime 60
 ip nhrp nhs 10.200.0.1
 ip tcp adjust-mss 1360
 tunnel source 95.188.50.2
 tunnel mode gre multipoint
 tunnel key 200
!
interface Tunnel20
 description DMVPN-SPOKE-R14-MSK
 ip address 10.120.0.3 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip nhrp map multicast 95.188.0.2
 ip nhrp map 10.120.0.1 95.188.0.2
 ip nhrp network-id 200
 ip nhrp nhs 10.120.0.1
 ip tcp adjust-mss 1360
 tunnel source 95.188.100.2
 tunnel mode gre multipoint
 tunnel key 120
```  
Tunnel10 подключается к R15, Tunnel20 подключается к R14. В случае отказа одного из маршрутизаторов связь между офисами будет.

Настройка DMVPN на R27 (Лабытнанги)  
```
interface Tunnel10
 description DMVPN-SPOKE
 ip address 10.200.0.5 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip nhrp map multicast 109.226.218.100
 ip nhrp map 10.200.0.1 109.226.218.100
 ip nhrp network-id 100
 ip nhrp holdtime 60
 ip nhrp nhs 10.200.0.1
 ip tcp adjust-mss 1360
 tunnel source Ethernet0/0
 tunnel mode gre multipoint
 tunnel key 200
!
interface Tunnel20
 description DMVPN-SPOKE
 ip address 10.120.0.5 255.255.255.0
 no ip redirects
 ip mtu 1400
 ip nhrp map multicast 95.188.0.2
 ip nhrp map 10.120.0.1 95.188.0.2
 ip nhrp network-id 200
 ip nhrp holdtime 60
 ip nhrp nhs 10.120.0.1
 ip tcp adjust-mss 1360
 tunnel source Ethernet0/0
 tunnel mode gre multipoint
 tunnel key 120
```  
Tunnel10 подключается к R15, Tunnel20 подключается к R14. В случае отказа одного из маршрутизаторов связь между офисами будет.

Проверяю DMVPN на маршрутизаторах  
![](https://github.com/merkelev/neteng/blob/main/labs/13-VPN-GRE-DMVPN/images/DMVPN.png)  

**Для связи между офисами настроил EIGRP.**  
Настройки EIGRP на R18 (Санкт-Петербург)  
```
router eigrp SP-EIGRP
 !
 address-family ipv4 unicast autonomous-system 4
  !
  topology base
   distribute-list route-map RM-FOR-EIGRP out Tunnel100
   redistribute bgp 2042 metric 10000000 30 255 20 1400
  exit-af-topology
  network 10.100.0.2 0.0.0.0
  network 172.22.18.1 0.0.0.0
  network 172.22.18.5 0.0.0.0
  eigrp router-id 172.22.44.1
 exit-address-family
 !
```  

Анонсирую в EIGRP только сеть 172.22.0.0/16    
```
ip prefix-list DO-NOT-SEND seq 5 deny 75.100.20.0/23
ip prefix-list DO-NOT-SEND seq 10 permit 172.22.0.0/16
!
route-map RM-FOR-EIGRP permit 10
 match ip address prefix-list DO-NOT-SEND
```  

Настройки EIGRP на R14 (Москва)  
```
router eigrp SP-EIGRP
 !
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Tunnel20
   no next-hop-self
   no split-horizon
  exit-af-interface
  !
  topology base
   redistribute ospf 10 metric 1000000 30 255 20 1400 route-map RM-FOR-EIGRP
  exit-af-topology
  network 10.120.0.1 0.0.0.0
  eigrp router-id 10.120.0.1
 exit-address-family
!
```  
Редистрибуция маршрутов OSPF в EIGRP  
```
route-map RM-FOR-EIGRP permit 10
 match ip address prefix-list LAN-FOR-TUNNEL
!
ip prefix-list LAN-FOR-TUNNEL seq 5 permit 172.18.12.0/25
ip prefix-list LAN-FOR-TUNNEL seq 10 permit 172.18.14.0/25
```  

Настройки EIGRP на R15 (Москва)  
```
router eigrp SP-EIGRP
 !
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Tunnel10
   no next-hop-self
   no split-horizon
  exit-af-interface
  !
  topology base
   redistribute ospf 10 metric 1000000 30 255 20 1400 route-map RM-FOR-EIGRP
  exit-af-topology
  network 10.100.0.0 0.0.0.255
  network 10.200.0.0 0.0.0.255
  eigrp router-id 172.18.10.15
 exit-address-family
!
```  
Редистрибуция маршрутов OSPF в EIGRP  
```
route-map RM-FOR-EIGRP permit 10
 match ip address prefix-list LAN-FOR-TUNNEL
!
ip prefix-list LAN-FOR-TUNNEL seq 5 permit 172.18.12.0/25
ip prefix-list LAN-FOR-TUNNEL seq 10 permit 172.18.14.0/25
```  

Проверяю соседство EIGRP  
![](https://github.com/merkelev/neteng/blob/main/labs/13-VPN-GRE-DMVPN/images/EIGRP-NEIG-R15-R18.png)  

Запускаю ping с узла VPC1 (Москва) до узла VPC8 (Санкт-Петербург)  
![](https://github.com/merkelev/neteng/blob/main/labs/13-VPN-GRE-DMVPN/images/PING-VPC1-TO-VPC8.png)  

Настройки OSPF на R28 (Чокурдах)  
```
router ospf 10
 router-id 10.200.0.3
 passive-interface default
 no passive-interface Ethernet0/2.32
 no passive-interface Ethernet0/2.34
 no passive-interface Tunnel10
 no passive-interface Tunnel20
!
```  

Настройки OSPF на R27 (Лабытнанги)  
```
router ospf 10
 router-id 10.200.0.2
 passive-interface default
 no passive-interface Ethernet0/1
 no passive-interface Tunnel10
 no passive-interface Tunnel20
!
```  

Проверяю соседство OSPF - R14, R15, R27 & R28  
![](https://github.com/merkelev/neteng/blob/main/labs/13-VPN-GRE-DMVPN/images/OSPF-R14-R15-R27-R28.png)  

Запускаю ping с узла VPC30 (Чокурдах) до узла VPC (Санкт-Петербург)  
![](https://github.com/merkelev/neteng/blob/main/labs/13-VPN-GRE-DMVPN/images/2021-06-27_17-05-23.png)  

Запускаю ping с узла VPC7 (Москва) до узла R27 (Лабытнанги). На R27 весит сеть 172.28.0.0/24 на Et0/1  
![](https://github.com/merkelev/neteng/blob/main/labs/13-VPN-GRE-DMVPN/images/2021-06-27_17-07-22.png)

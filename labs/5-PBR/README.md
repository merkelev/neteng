**PBR**  

**Цель: Настроить политику маршрутизации в офисе Чокурдах Распределить трафик между 2 линками**  

**Схема сети в Чокурдах**  
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/NET-CHOKURDAH.png)

Для п. Чокурдах использована сеть - 172.20.0.0/16  

**Таблица адресов IPv4 в п. Чокурдах**  
| Device   | Interface     | IP Address  | Subnet Mask     | Default Gateway | Description   |
| -------- | ------------- | --------    | --------        | --------        | ------------  |
| R28      | Et0/1         | 10.0.0.6    | 255.255.255.252 | 10.0.0.5        | to R25:et0/3  |
|          | Et0/0         | 10.0.1.6    | 255.255.255.252 | 10.0.1.5        | to R26:et0/1  |
|          | Et0/2         |             |                 |                 | to SW29:et0/2 |
|          | Et0/2.32      | 172.20.32.1 | 255.255.255.248 |                 |               |
|          | Et0/2.34      | 172.20.34.1 | 255.255.255.248 |                 |               |
|          | Et0/2.99      | 172.20.99.1 | 255.255.255.248 |                 |               |
| SW29     | VLAN99        | 172.20.99.4 | 255.255.255.248 | 172.20.99.1     |               |
| VPC30    | NIC           | 172.20.32.2 | 255.255.255.248 | 172.20.32.1     |               |
| VPC31    | NIC           | 172.20.34.2 | 255.255.255.248 | 172.20.34.1     |               |

**Таблица VLAN п. Чокурдах**  
| VLAN | Name     | Interface Assigned |
| ---- | -----    | ------------------ |
| 99   | MNGM     | SW29: VLAN 99      |
| 32   | FAU      | SW29: Et0/0        |
| 34   | LAWYERS  | SW29: Et0/1        |
| 54   | PRK-LOT  | SW29: Et0/3        |

**Конфигурации рабочих станций**  
**VPC30**  
```
NAME   IP/MASK              GATEWAY                             GATEWAY
VPCS1  172.20.32.2/29       172.20.32.1
       fe80::250:79ff:fe66:681e/64
```  

**VPC31**  
```
NAME   IP/MASK              GATEWAY                             GATEWAY
VPCS1  172.20.34.2/29       172.20.34.1
       fe80::250:79ff:fe66:681f/64
```  

**Конфигурация коммутатора SW29**  
```
Current configuration : 1038 bytes
!
! Last configuration change at 14:07:38 +07 Tue Apr 13 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW29
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
ip cef
no ipv6 cef
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
interface Ethernet0/0
 description TO-VPC30
 switchport access vlan 32
 switchport mode access
!
interface Ethernet0/1
 description TO-VPC31
 switchport access vlan 34
 switchport mode access
!
interface Ethernet0/2
 switchport trunk allowed vlan 32,34,99
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
end
```  

**Конфигурация маршрутизатора R28**  
```
Current configuration : 2877 bytes
!
! Last configuration change at 11:32:03 +07 Tue Apr 20 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R28
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
track 1 ip sla 1 reachability
!
track 2 ip sla 2 reachability
!
interface Ethernet0/0
 description TO-ISP-1
 ip address 10.0.1.6 255.255.255.252
 ip nat outside
 ip virtual-reassembly in
!
interface Ethernet0/1
 description TO-ISP-2
 ip address 10.0.0.6 255.255.255.252
 ip nat outside
 ip virtual-reassembly in
!
interface Ethernet0/2
 no ip address
!
interface Ethernet0/2.32
 encapsulation dot1Q 32
 ip address 172.20.32.1 255.255.255.248
 ip nat inside
 ip virtual-reassembly in
 ip policy route-map PBR
!
interface Ethernet0/2.34
 encapsulation dot1Q 34
 ip address 172.20.34.1 255.255.255.248
 ip nat inside
 ip virtual-reassembly in
 ip policy route-map PBR
!
interface Ethernet0/2.99
 encapsulation dot1Q 99
 ip address 172.20.99.1 255.255.255.248
!
interface Ethernet0/3
 no ip address
 shutdown
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
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip nat translation timeout 5
ip nat inside source route-map RM-ISP-1 interface Ethernet0/0 overload
ip nat inside source route-map RM-ISP-2 interface Ethernet0/1 overload
ip route 0.0.0.0 0.0.0.0 10.0.1.5 track 1
ip route 0.0.0.0 0.0.0.0 10.0.0.5 10 track 2
!
ip access-list extended ACL-NAT-32
 permit ip 172.20.32.0 0.0.0.7 any
ip access-list extended ACL-NAT-34
 permit ip 172.20.34.0 0.0.0.7 any
!
ip sla 1
 icmp-echo 10.0.1.5 source-ip 10.0.1.6
 frequency 30
ip sla schedule 1 life forever start-time now
ip sla 2
 icmp-echo 10.0.0.5 source-ip 10.0.0.6
 frequency 30
ip sla schedule 2 life forever start-time now
!
route-map PBR permit 10
 match ip address ACL-NAT-32
 set ip next-hop verify-availability 10.0.1.5 1 track 1
 set ip next-hop verify-availability 10.0.0.5 2 track 2
!
route-map PBR permit 20
 match ip address ACL-NAT-34
 set ip next-hop verify-availability 10.0.0.5 1 track 2
 set ip next-hop verify-availability 10.0.1.5 2 track 1
!
route-map RM-ISP-1 permit 10
 match ip address ACL-NAT-32 ACL-NAT-34
 match interface Ethernet0/0
!
route-map RM-ISP-2 permit 10
 match ip address ACL-NAT-34 ACL-NAT-32
 match interface Ethernet0/1
!
end
```  

Настроил маршрутные карты для NAT  
```
route-map RM-ISP-1 permit 10
 match ip address ACL-NAT-32 ACL-NAT-34
 match interface Ethernet0/0
!
route-map RM-ISP-2 permit 10
 match ip address ACL-NAT-32 ACL-NAT-34
 match interface Ethernet0/1
!
```  

Настроил маршрутную карту для PBR и привязал к интерфейсам Ethernet0/2.32, Ethernet0/2.34  
```
route-map PBR permit 10
 match ip address ACL-NAT-32
 set ip next-hop verify-availability 10.0.1.5 1 track 1
 set ip next-hop verify-availability 10.0.0.5 2 track 2
!
route-map PBR permit 20
 match ip address ACL-NAT-34
 set ip next-hop verify-availability 10.0.0.5 1 track 2
 set ip next-hop verify-availability 10.0.1.5 2 track 1
!
```  

Трафик сети 172.20.32.0/29 (VLAN32) идет через первого провайдера (интерфейс Et0/0). Трафик сети 172.20.34.0/29 (VLAN34) идет через второго провайдера (интерфейс Et0/1). В случае отказа первого провайдера, трафик сети 172.20.32.0/29 идет через второго провайдера, а в случае отказа второго провайдера трафик сети 172.20.34.0/29 идет через первого провайдера.  

Маршруты и NAT  
```
ip nat inside source route-map RM-ISP-1 interface Ethernet0/0 overload
ip nat inside source route-map RM-ISP-2 interface Ethernet0/1 overload
ip route 0.0.0.0 0.0.0.0 10.0.1.5 track 1
ip route 0.0.0.0 0.0.0.0 10.0.0.5 10 track 2
```  

Настроил отслеживание линков через IP SLA    
```
ip sla 1
 icmp-echo 10.0.1.5 source-ip 10.0.1.6
 frequency 30
ip sla schedule 1 life forever start-time now
ip sla 2
 icmp-echo 10.0.0.5 source-ip 10.0.0.6
 frequency 30
ip sla schedule 2 life forever start-time now
!
```  

Настроил трэки  
```
R28#sh track
Track 1
  IP SLA 1 reachability
  Reachability is Up
    4 changes, last change 00:21:22
  Latest operation return code: OK
  Latest RTT (millisecs) 1
  Tracked by:
    Route Map 0
    Static IP Routing 0
Track 2
  IP SLA 2 reachability
  Reachability is Up
    4 changes, last change 00:21:37
  Latest operation return code: OK
  Latest RTT (millisecs) 1
  Tracked by:
    Route Map 0
    Static IP Routing 0
```  
Настроил маршрут по умолчанию для Лабытнанги  
```
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```  
IP адрес на интерфейсе Et0/0 маршрутизатора R27 - 10.0.0.2  

```
  Current configuration : 1189 bytes
!
! Last configuration change at 15:57:43 +07 Tue Apr 13 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R27
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
 description TO-ISP-TRIADA
 ip address 10.0.0.2 255.255.255.252
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
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 10.0.0.1
!
end
```  
**Для проверки работы Route-map, PBR, IP SLA я настроил на машрутизаторах провайдера "Триада" (R25, R26) ip адреса.** 
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/NET-TRIADA.png)  

**Конфигурация маршрутизатора R25**  
```
Current configuration : 1259 bytes
!
! Last configuration change at 15:44:34 +07 Tue Apr 13 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R25
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
 no ip address
 shutdown
!
interface Ethernet0/1
 description TO-CLIENT-LABYTNANGI
 ip address 10.0.0.1 255.255.255.252
!
interface Ethernet0/2
 ip address 10.0.1.1 255.255.255.252
!
interface Ethernet0/3
 description TO-CLIENT-CHOK-1
 ip address 10.0.0.5 255.255.255.252
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
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 10.0.1.4 255.255.255.252 10.0.1.2
!
end
```  
**Таблица маршрутизации R25**  
```
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
C        10.0.0.0/30 is directly connected, Ethernet0/1
L        10.0.0.1/32 is directly connected, Ethernet0/1
C        10.0.0.4/30 is directly connected, Ethernet0/3
L        10.0.0.5/32 is directly connected, Ethernet0/3
C        10.0.1.0/30 is directly connected, Ethernet0/2
L        10.0.1.1/32 is directly connected, Ethernet0/2
S        10.0.1.4/30 [1/0] via 10.0.1.2
```  

**Конфигурация маршрутизатора R26**  
```
Current configuration : 1208 bytes
!
! Last configuration change at 16:43:15 +07 Tue Apr 13 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R26
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
 no ip address
 shutdown
!
interface Ethernet0/1
 description TO-CLIENT-2
 ip address 10.0.1.5 255.255.255.252
!
interface Ethernet0/2
 ip address 10.0.1.2 255.255.255.252
!
interface Ethernet0/3
 no ip address
 shutdown
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
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 10.0.0.0 255.255.255.252 10.0.1.1
!
end
```  
**Таблица маршрутизации R26**  
```
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 5 subnets, 2 masks
S        10.0.0.0/30 [1/0] via 10.0.1.1
C        10.0.1.0/30 is directly connected, Ethernet0/2
L        10.0.1.2/32 is directly connected, Ethernet0/2
C        10.0.1.4/30 is directly connected, Ethernet0/1
L        10.0.1.5/32 is directly connected, Ethernet0/1
```  

**Запускаю пинг с рабочей станции VPC31 до маршрутизатора R27**  
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/PINGVPC31-TO-R27.png)  

**Запускаю пинг с рабочей станции VPC30 до маршрутизатора R27**  
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/PING-VPC30-TO-R27.png)

Как видим хост VPC30 идет через первого провайдера, хост VPC31 идет через второго провайдера.  

**Таблица маршрутизации на R28**  
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/IP-ROUTE-R28-BEFORE.png)  

**Отключаю интерфейс Et0/1 на маршрутизаторе R26. Проверяем IP SLA и track на R28**  
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/SH-IP-SLA-TRACK.png)  

**Смотрим таблицу маршрутизации на R28**  
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/IP-ROUTE-R28-AFTER.png)  

**Видим что дефолтный шлюз изменился.  
Проверяю доступность R27 запустив пинг с рабочей станции VPC31 и VPC30**  
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/PING-VPC30-VPC31-TO-R27.png)  

Обе сети идут через второго провайдера.  

**Включаю интерфейс Et0/1 на маршрутизаторе R26. Проверяю IP SLA, track и дефолтный маршрут на маршрутизаторе R28**   
![](https://github.com/merkelev/neteng/blob/main/labs/5-PBR/IP-SLA-TRACK-IP-ROUTE-R28-AFTER.png)  

### EIGRP

**Цель: Настроить EIGRP в С.-Петербург. Использовать named EIGRP.**  
1. R32 получает только маршрут по-умолчанию  
2. R16-17 анонсируют только суммарные префиксы  

**Схема сети в г. Санкт-Петербург**  
![](https://github.com/merkelev/neteng/blob/main/labs/8-EIGRP/NET-SP.png)  

**Таблица IPv6 link-local адресов**  
| Device | Interface | Address   |
| ------ | --------- | -------   |
| R18    | Et0/0     | FE80::1   |
|        | Et0/1     | FE80::A:1 |
| R32    | Et0/0     | FE80::B:1 |
| R16    | Et0/2     | FE80::B:2 |
|        | Et0/1     | FE80::2   |
|        | Et0/3     | FE80::C:2 |
|        | Et0/0.99  | FE80::99:1|
|        | Et0/0.24  | FE80::24:1|
|        | Et0/0.22  | FE80::22:1|
| R17    | Et0/1     | FE80::A:2 |
|        | Et0/3     | FE80::C:1 |
|        | Et0/0.99  | FE80::99:2|
|        | Et0/0.24  | FE80::24:2|
|        | Et0/0.22  | FE80::22:2|

**Таблица Router-ID EIGRP IPv4 & IPv6 маршрутизаторов**  
| Device | Router-id   |
| ------ | ------------|
| R18    | 172.22.44.1 |
| R17    | 172.22.44.2 |
| R16    | 172.22.44.3 |
| R32    | 172.22.44.4 |

**Конфигурация маршрутизатора R18**  
```
Current configuration : 1637 bytes
!
! Last configuration change at 14:45:11 +07 Fri May 14 2021
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
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
interface Ethernet0/0
 description TO-R16-ET0/1
 ip address 172.22.18.1 255.255.255.252
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::18:5/126
!
interface Ethernet0/1
 description TO-R17-ET0/1
 ip address 172.22.18.5 255.255.255.252
 ipv6 address FE80::A:1 link-local
 ipv6 address 2001:DB8:ACAD:1::18:9/126
!
router eigrp SP-EIGRP
 !
 address-family ipv4 unicast autonomous-system 4
  !
  topology base
   redistribute static
  exit-af-topology
  network 172.22.18.0 0.0.0.3
  network 172.22.18.4 0.0.0.3
  eigrp router-id 172.22.44.1
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 6
  !
  topology base
   redistribute static
  exit-af-topology
  eigrp router-id 172.22.44.1
 exit-address-family
!
ip forward-protocol nd
!
ip route 0.0.0.0 0.0.0.0 Null0
!
ipv6 route ::/0 Null0
!
end
```  

**Конфигурация маршрутизатора R32**  
```
Current configuration : 1395 bytes
!
! Last configuration change at 15:03:56 +07 Fri May 14 2021
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
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R16-ET0/2
 ip address 172.22.18.10 255.255.255.252
 ipv6 address FE80::B:1 link-local
 ipv6 address 2001:DB8:ACAD:1::18:2/126
!
router eigrp SP-EIGRP
 !
 address-family ipv4 unicast autonomous-system 4
  !
  topology base
  exit-af-topology
  network 172.22.18.8 0.0.0.3
  eigrp router-id 172.22.44.4
 exit-address-family
 !
 address-family ipv6 unicast autonomous-system 6
  !
  topology base
  exit-af-topology
  eigrp router-id 172.22.44.4
 exit-address-family
!
ip forward-protocol nd
!
end
```  

**Конфигурация маршрутизатора R16**  
```
Building configuration...

Current configuration : 3016 bytes
!
! Last configuration change at 15:04:20 +07 Fri May 14 2021
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
 ipv6 address FE80::22:1 link-local
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
 ipv6 address FE80::24:1 link-local
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
 ipv6 address FE80::99:1 link-local
 ipv6 address 2001:DB8:ACAD:1::99:2/120
!
interface Ethernet0/1
 description TO-R18-ET0/0
 ip address 172.22.18.2 255.255.255.252
 ipv6 address FE80::2 link-local
 ipv6 address 2001:DB8:ACAD:1::18:6/126
!
interface Ethernet0/2
 description TO-R32-ET0/0
 ip address 172.22.18.9 255.255.255.252
 ipv6 address FE80::B:2 link-local
 ipv6 address 2001:DB8:ACAD:1::18:1/126
!
interface Ethernet0/3
 description TO-R17-ET0/3
 ip address 172.22.18.13 255.255.255.252
 ipv6 address FE80::C:2 link-local
 ipv6 address 2001:DB8:ACAD:1::18:D/126
!
router eigrp SP-EIGRP
 !
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Ethernet0/1
   summary-address 172.22.0.0 255.255.0.0
  exit-af-interface
  !
  topology base
   distribute-list prefix EIGRP-TO-R32 out Ethernet0/2
  exit-af-topology
  network 172.22.18.0 0.0.0.3
  network 172.22.18.8 0.0.0.3
  network 172.22.18.12 0.0.0.3
  eigrp router-id 172.22.44.3
 exit-address-family
 !
  address-family ipv6 unicast autonomous-system 6
  !
  af-interface Ethernet0/1
   summary-address 2001:DB8:ACAD:1::/64
  exit-af-interface
  !
  topology base
   distribute-list prefix-list EIGRP-IPV6-TO-R32 out Ethernet0/2
  exit-af-topology
  eigrp router-id 172.22.44.3
 exit-address-family
!
ip prefix-list EIGRP-TO-R32 seq 5 deny 172.22.0.0/16 ge 25 le 30
ip prefix-list EIGRP-TO-R32 seq 10 permit 0.0.0.0/0 le 32
!
ipv6 prefix-list EIGRP-IPV6-TO-R32 seq 5 deny 2001:DB8:ACAD:1::/64
ipv6 prefix-list EIGRP-IPV6-TO-R32 seq 10 permit ::/0
!
end
```  

**Конфигурация маршрутизатора R17**  
```
Current configuration : 2564 bytes
!
! Last configuration change at 14:59:20 +07 Fri May 14 2021
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
 ipv6 address FE80::22:2 link-local
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
 ipv6 address FE80::24:2 link-local
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
 ipv6 address FE80::99:2 link-local
 ipv6 address 2001:DB8:ACAD:1::99:3/120
!
interface Ethernet0/1
 description TO-R18-ET0/1
 ip address 172.22.18.6 255.255.255.252
 ipv6 address FE80::A:2 link-local
 ipv6 address 2001:DB8:ACAD:1::18:A/126
!
interface Ethernet0/3
 description TO-R16-ET0/3
 ip address 172.22.18.14 255.255.255.252
 ipv6 address FE80::C:1 link-local
 ipv6 address 2001:DB8:ACAD:1::18:E/126
!
router eigrp SP-EIGRP
 !
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Ethernet0/1
   summary-address 172.22.0.0 255.255.0.0
  exit-af-interface
  !
  topology base
  exit-af-topology
  network 172.22.18.4 0.0.0.3
  network 172.22.18.12 0.0.0.3
  eigrp router-id 172.22.44.2
 exit-address-family
 !
  address-family ipv6 unicast autonomous-system 6
  !
  af-interface Ethernet0/1
   summary-address 2001:DB8:ACAD:1::/64
  exit-af-interface
  !
  topology base
  exit-af-topology
  eigrp router-id 172.22.44.2
 exit-address-family
!
end
```  

**1. По условиям задачи маршрутизатор R32 должен получать только маршрут по умолчанию**  
На маршрутизаторе R16 настроил prefix-list's для IPv4 & IPv6  
```
ip prefix-list EIGRP-TO-R32 seq 5 deny 172.22.0.0/16 ge 25 le 30
ip prefix-list EIGRP-TO-R32 seq 10 permit 0.0.0.0/0 le 32
!
ipv6 prefix-list EIGRP-IPV6-TO-R32 seq 5 deny 2001:DB8:ACAD:1::/64
ipv6 prefix-list EIGRP-IPV6-TO-R32 seq 10 permit ::/0
```  
И назначил листы на направление out интерфейса Ethernet 0/2 маршрутизатора R16  
```
 address-family ipv4 unicast autonomous-system 4
  !
  topology base
   distribute-list prefix EIGRP-TO-R32 out Ethernet0/2
  exit-af-topology

address-family ipv6 unicast autonomous-system 6
  !
  topology base
   distribute-list prefix-list EIGRP-IPV6-TO-R32 out Ethernet0/2
  exit-af-topology
```  

**Проверям таблицу маршрутизации на R32**   
![](https://github.com/merkelev/neteng/blob/main/labs/8-EIGRP/ROUTE-IPV4-IPV6-R32.png)  
**На маршрутизатор R32 приходит только маршрут по умолчанию**  

**2. По условия задачи маршрутизаторы R16 & R17 должны анонсировать только суммарные префиксы**  
Настроил суммаризацию на интерфейсах Ethernet0/1 маршрутизаторов R16 & R17 смотрящих в сторону соседа - маршрутизатора R18   
**R16**  
```
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Ethernet0/1
   summary-address 172.22.0.0 255.255.0.0
  exit-af-interface
  
 address-family ipv6 unicast autonomous-system 6
  !
  af-interface Ethernet0/1
   summary-address 2001:DB8:ACAD:1::/64
  exit-af-interface
```  
**R17**  
```
 address-family ipv4 unicast autonomous-system 4
  !
  af-interface Ethernet0/1
   summary-address 172.22.0.0 255.255.0.0
  exit-af-interface

address-family ipv6 unicast autonomous-system 6
  !
  af-interface Ethernet0/1
   summary-address 2001:DB8:ACAD:1::/64
  exit-af-interface
```  

**Проверяем таблицу маршрутизации на R18**  
![](https://github.com/merkelev/neteng/blob/main/labs/8-EIGRP/ROUTE-IPV4-IPV6-R18.png)  

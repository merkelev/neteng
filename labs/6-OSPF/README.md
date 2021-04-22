### OSPF

**Цель: Настроить OSPF офисе Москва Разделить сеть на зоны. Настроить фильтрацию между зонами**  
1. Маршрутизаторы R14-R15 находятся в зоне 0 - backbone  
2. Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по-умолчанию  
3. Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию  
4. Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101  

**Схема сети**  
![](https://github.com/merkelev/neteng/blob/main/labs/6-OSPF/NET-MOSCOW.png)  

**1. Настроил OSPF на маршрутизаторах R14 и R15 для зоны 0**  
**Настройки OSPF IPv4 и IPv6 на маршрутизаторе R14**  
```
interface Ethernet0/0
 description TO-R12-ET0/2
 ip address 172.18.0.5 255.255.255.252
 ip ospf 10 area 0
 ipv6 address 2001:DB8:ACAD:2::18:5/126
 ipv6 ospf 10 area 0
!
interface Ethernet0/1
 description TO-R13-ET0/3
 ip address 172.18.0.9 255.255.255.252
 ip ospf 10 area 0
 ipv6 address 2001:DB8:ACAD:2::18:9/126
 ipv6 ospf 10 area 0
!
interface Ethernet0/2
 no ip address
!
interface Ethernet0/3
 description TO-R19-ET0/0
 ip address 172.18.0.1 255.255.255.252
 ip ospf 10 area 101
 ipv6 address 2001:DB8:ACAD:2::18:2/126
 ipv6 ospf 10 area 101
!
router ospf 10
 router-id 172.18.0.1
 area 101 stub no-summary
 passive-interface default
 no passive-interface Ethernet0/0
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/3
 default-information originate always
!
ipv6 router ospf 10
!
```  

Интерфейс Et0/0 и Et0/1 находятся в зоне 0. Интерфейс Et0/3 находиться в зоне 101. Для зоны 0 маршрутизатор является ABR.  

**Настройки OSPF IPv4 и IPv6 на маршрутизаторе R15**  
```
interface Ethernet0/0
 description TO-R13-ET0/2
 ip address 172.18.0.17 255.255.255.252
 ip ospf 10 area 0
 ipv6 address 2001:DB8:ACAD:2::18:12/126
 ipv6 ospf 10 area 0
!
interface Ethernet0/1
 description TO-R12-ET0/3
 ip address 172.18.0.13 255.255.255.252
 ip ospf 10 area 0
 ipv6 address 2001:DB8:ACAD:2::18:19/126
 ipv6 ospf 10 area 0
!
interface Ethernet0/2
 no ip address
!
interface Ethernet0/3
 description TO-R20-ET0/0
 ip address 172.18.0.21 255.255.255.252
 ip ospf priority 10
 ip ospf 10 area 102
 ipv6 address 2001:DB8:ACAD:2::18:15/126
 ipv6 ospf 10 area 102
 ipv6 ospf priority 10
!
router ospf 10
 router-id 172.18.0.21
 area 102 filter-list prefix FILTER-TO-R20 in
 passive-interface default
 no passive-interface Ethernet0/0
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/3
 default-information originate always
!
ip prefix-list FILTER-TO-R20 seq 5 deny 172.18.0.0/30
ip prefix-list FILTER-TO-R20 seq 10 permit 0.0.0.0/0 le 32
ipv6 router ospf 10
!
```  

Интерфейс Et0/0 и Et0/1 находятся в зоне 0. Интерфейс Et0/3 находиться в зоне 102. Для зоны 0 маршрутизатор является ABR.

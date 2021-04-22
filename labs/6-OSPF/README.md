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

Интерфейс Et0/0 и Et0/1 находятся в зоне 0. Интерфейс Et0/3 находиться в зоне 101. 
Таблица маршрутизации IPv4 и IPv6:  
![](https://github.com/merkelev/neteng/blob/main/labs/6-OSPF/IP-ROUTE-R14.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/6-OSPF/IPv6-ROUTE-R14.png)  

Соседство OSPF IPv4 и IPv6:  
![](https://github.com/merkelev/neteng/blob/main/labs/6-OSPF/OSPF-R14.png)  

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

Интерфейс Et0/0 и Et0/1 находятся в зоне 0. Интерфейс Et0/3 находиться в зоне 102.

**2. Настроил OSPF на маршрутизаторах R12 и R13 для зоны 0 и 10**  
**Настройки OSPF IPv4 и IPv6 на маршрутизаторе R12**  
```
interface Ethernet0/0.9
 encapsulation dot1Q 9
 ip address 172.18.9.2 255.255.255.128
 standby version 2
 standby 9 ip 172.18.9.1
 standby 9 priority 120
 standby 209 ipv6 2001:DB8:ACAD:2::9:1/120
 standby 209 priority 120
 ip ospf 10 area 10
 ipv6 address 2001:DB8:ACAD:2::9:2/120
 ipv6 ospf 10 area 10
!
interface Ethernet0/0.12
 encapsulation dot1Q 12
 ip address 172.18.12.2 255.255.255.128
 standby version 2
 standby 12 ip 172.18.12.1
 standby 12 priority 120
 standby 212 ipv6 2001:DB8:ACAD:2::12:1/120
 standby 212 priority 120
 ip ospf 10 area 10
 ipv6 address 2001:DB8:ACAD:2::12:2/120
 ipv6 ospf 10 area 10
!
interface Ethernet0/0.14
 encapsulation dot1Q 14
 ip address 172.18.14.2 255.255.255.128
 standby version 2
 standby 14 ip 172.18.14.1
 standby 14 priority 120
 standby 214 ipv6 2001:DB8:ACAD:2::14:1/120
 standby 214 priority 120
 ip ospf 10 area 10
 ipv6 address 2001:DB8:ACAD:2::14:2/120
 ipv6 ospf 10 area 10
!
interface Ethernet0/1
 description TO-SW5-ET0/2
 ip address 172.18.0.26 255.255.255.252
 ip ospf 10 area 10
 ipv6 address 2001:DB8:ACAD:2::18:D/126
 ipv6 ospf 10 area 10
!
interface Ethernet0/2
 description TO-R14-ET0/0
 ip address 172.18.0.6 255.255.255.252
 ip ospf 10 area 0
 ipv6 address 2001:DB8:ACAD:2::18:6/126
 ipv6 ospf 10 area 0
!
interface Ethernet0/3
 description TO-R15-ET0/1
 ip address 172.18.0.14 255.255.255.252
 ip ospf 10 area 0
 ipv6 address 2001:DB8:ACAD:2::18:1A/126
 ipv6 ospf 10 area 0
!
router ospf 10
 router-id 172.18.0.14
 passive-interface default
 no passive-interface Ethernet0/0.12
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/2
 no passive-interface Ethernet0/3
!
ipv6 router ospf 10
!
```  
Интерфейс Et0/0.9, Et0/0.12, Et0/0.14 и Et0/1 находятся в зоне 10. Интерфейс Et0/2, Et0/3 находятся в зоне 0.  
**По условиям задачи маршрутизатор получает маршрут по умолчанию.**  
![](https://github.com/merkelev/neteng/blob/main/labs/6-OSPF/IP-ROUTE-R12.png)  

**Настройки OSPF IPv4 и IPv6 на маршрутизаторе R13**  
```
interface Ethernet0/0.9
 encapsulation dot1Q 9
 ip address 172.18.9.3 255.255.255.128
 standby version 2
 standby 9 ip 172.18.9.1
 standby 9 preempt
 standby 209 ipv6 2001:DB8:ACAD:2::9:1/120
 standby 209 preempt
 ip ospf 10 area 10
 ipv6 address 2001:DB8:ACAD:2::9:3/120
 ipv6 ospf 10 area 10
!
interface Ethernet0/0.12
 encapsulation dot1Q 12
 ip address 172.18.12.3 255.255.255.128
 standby version 2
 standby 12 ip 172.18.12.1
 standby 12 preempt
 standby 212 ipv6 2001:DB8:ACAD:2::12:1/120
 standby 212 preempt
 ip ospf 10 area 10
 ipv6 address 2001:DB8:ACAD:2::12:3/120
 ipv6 ospf 10 area 10
!
interface Ethernet0/0.14
 encapsulation dot1Q 14
 ip address 172.18.14.3 255.255.255.128
 standby version 2
 standby 14 ip 172.18.14.1
 standby 14 preempt
 standby 214 ipv6 2001:DB8:ACAD:2::14:1/120
 standby 214 preempt
 ip ospf 10 area 10
 ipv6 address 2001:DB8:ACAD:2::14:3/120
 ipv6 ospf 10 area 10
!
interface Ethernet0/1
 description TO-SW4-ET0/2
 ip address 172.18.0.25 255.255.255.252
 ip ospf 10 area 10
 ipv6 address 2001:DB8:ACAD:2::18:E/126
 ipv6 ospf 10 area 10
!
interface Ethernet0/2
 description TO-R15-ET0/0
 ip address 172.18.0.18 255.255.255.252
 ip ospf 10 area 0
 ipv6 address 2001:DB8:ACAD:2::18:11/126
 ipv6 ospf 10 area 0
!
interface Ethernet0/3
 description TO-R14-ET0/1
 ip address 172.18.0.10 255.255.255.252
 ip ospf 10 area 0
 ipv6 address 2001:DB8:ACAD:2::18:A/126
 ipv6 ospf 10 area 0
!
router ospf 10
 router-id 172.18.0.10
 passive-interface default
 no passive-interface Ethernet0/1
 no passive-interface Ethernet0/2
 no passive-interface Ethernet0/3
!
ipv6 router ospf 10
!
```  
Интерфейс Et0/0.9, Et0/0.12, Et0/0.14 и Et0/1 находятся в зоне 10. Интерфейс Et0/2, Et0/3 находятся в зоне 0.  
**По условиям задачи маршрутизатор получает маршрут по умолчанию.**  
![](https://github.com/merkelev/neteng/blob/main/labs/6-OSPF/IP-ROUTE-R13.png)  

**3. Настроил OSPF на маршрутизаторе R19 для зоны 101.**  
**Настройки IPv4 и IPv6 на маршрутизаторе R19.**  
```
interface Ethernet0/0
 description TO-R14-ET0/3
 ip address 172.18.0.2 255.255.255.252
 ip ospf 10 area 101
 ipv6 address 2001:DB8:ACAD:2::18:1/126
 ipv6 ospf 10 area 101
!
router ospf 10
 router-id 172.18.0.2
 area 101 stub
 passive-interface default
 no passive-interface Ethernet0/0
!
ipv6 router ospf 10
!
```  
Интерфейс Et0/0 находятся в stub зоне 101 и получает только маршрут по умолчанию.
![](https://github.com/merkelev/neteng/blob/main/labs/6-OSPF/IP-ROUTE-R19.png)  

**4. Настроил OSPF на маршрутизаторе R20 для зоны 102.**  
**Настройки OSPF IPv4 и IPv6 на маршрутизаторе R20.**  
```
interface Ethernet0/0
 description TO-R15-ET0/3
 ip address 172.18.0.22 255.255.255.252
 ip ospf 10 area 102
 ipv6 address 2001:DB8:ACAD:2::18:16/126
 ipv6 ospf 10 area 102
!
router ospf 10
 router-id 172.18.0.22
 passive-interface default
 no passive-interface Ethernet0/0
!
ipv6 router ospf 10
!
```  

По условиям задачи настроил фильтрацию маршрутов, что-бы R20 получал все маршруты, кроме маршрутов до сетей зоны 101 (сеть 172.18.0.0/30)  
На R15 настроил префикс-листы:
```
ip prefix-list FILTER-TO-R20 seq 5 deny 172.18.0.0/30
ip prefix-list FILTER-TO-R20 seq 10 permit 0.0.0.0/0 le 32
```  
и назначил фильтр на зону 102:  
```
area 102 filter-list prefix FILTER-TO-R20 in
```  

Как видим фильтрация выполняется и маршрут до сети 172.18.0.0/30 не присутствует в таблице маршрутизатора R20:  
![](https://github.com/merkelev/neteng/blob/main/labs/6-OSPF/IP-ROUTE-R20.png)  

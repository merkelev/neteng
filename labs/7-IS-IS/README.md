### IS-IS
**Цель: Настроить IS-IS ISP Триада**    
**1. R23 и R25 находятся в зоне 2222**  
**2. R24 находится в зоне 24**  
**3. R26 находится в зоне 26**  

**Схема сети ISP Триада**  
![](https://github.com/merkelev/neteng/blob/main/labs/7-IS-IS/NET-ISP-TRIADA2.png)  

Для ISP Триада использованы адреса IPv4 - 172.24.0.0/16 и IPv6 - 2001:DB8:ACAD:3::0/64  

**Таблица адресов IPv4 ISP Триада**  
| Device   | Interface     | IP Address  | Subnet Mask | Default Gateway | Description  |
| -------- | ------------- | --------    | --------    | --------        | ------------ |
| R25      | Et0/3         | 10.0.0.5    | 255.255.255.252 |             | to R28:et0/1 |
|          | Et0/2         | 172.24.0.1  | 255.255.255.252 |             | to R26:et0/2 |
|          | Et0/1         | 10.0.0.1    | 255.255.255.252 |             | to R27:et0/0 |
|          | Et0/0         | 172.24.0.5  | 255.255.255.252 |             | to R23:et0/1 |
| R26      | Et0/2         | 172.24.0.2  | 255.255.255.252 |             | to R25:et0/2 |
|          | Et0/1         | 10.0.0.5    | 255.255.255.252 |             | to R28:et0/0 |
|          | Et0/0         | 172.24.0.9  | 255.255.255.252 |             | to R24:et0/1 |
| R24      | Et0/1         | 172.24.0.10 | 255.255.255.252 |             | to R26:et0/0 |
|          | Et0/2         | 172.24.0.13 | 255.255.255.252 |             | to R23:et0/2 | 
| R23      | Et0/1         | 172.24.0.6  | 255.255.255.252 |             | to R25:et0/0 |
|          | Et0/2         | 172.24.0.14 | 255.255.255.252 |             | to R24:et0/2 | 

**Таблица адресов IPv6 ISP Триада**  
| Device   | Interface     |  IPv6 Address          |Prefix| Default Gateway        |  Description | 
| -------- | ------------- |   --------             | ---  | --------               |  ------------|
| R25      | Et0/0         | 2001:DB8:ACAD:3::24:1  | /126 |                        | to R23:et0/1 |
|          | Et0/2         | 2001:DB8:ACAD:3::24:E  | /126 |                        | to R26:et0/2 |
| R26      | Et0/0         | 2001:DB8:ACAD:3::24:A  | /126 |                        | to R23:et0/1 |
|          | Et0/2         | 2001:DB8:ACAD:3::24:D  | /126 |                        | to R25:et0/2 |
| R24      | Et0/2         | 2001:DB8:ACAD:3::24:6  | /126 |                        | to R23:et0/2 |
|          | Et0/1         | 2001:DB8:ACAD:3::24:9  | /126 |                        | to R26:et0/0 |
| R23      | Et0/1         | 2001:DB8:ACAD:3::24:2  | /126 |                        | to R25:et0/0 |
|          | Et0/2         | 2001:DB8:ACAD:3::24:5  | /126 |                        | to R24:et0/2 |

**Таблица зон и NET адресов**  
| Device   | AREA  | NET            |
| -------- | ----- | -------------- |
| R25      | 2222  | 0001.0001.0001 |
| R23      | 2222  | 0002.0002.0002 |
| R24      | 0024  | 0024.0024.0001 |
| R26      | 0026  | 0026.0026.0001 |


**1. Настроил IS-IS на маршрутизаторах R23 & R25**  
Конфигурация маршрутизатора R23  
```
Current configuration : 1340 bytes
!
! Last configuration change at 14:48:58 +07 Thu Apr 29 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R23
!
boot-start-marker
boot-end-marker
!
!
!
no aaa new-model
clock timezone +07 7 0
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
ip cef
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
interface Ethernet0/1
 ip address 172.24.0.6 255.255.255.252
 ip router isis
 ipv6 address 2001:DB8:ACAD:3::24:2/126
 ipv6 router isis
!
interface Ethernet0/2
 ip address 172.24.0.14 255.255.255.252
 ip router isis
 ipv6 address 2001:DB8:ACAD:3::24:5/126
 ipv6 router isis
!
router isis
 net 49.2222.0002.0002.0002.00
!
end
```  

Конфигурация маршрутизатора R25  
```
Current configuration : 1481 bytes
!
! Last configuration change at 14:49:01 +07 Thu Apr 29 2021
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
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
interface Ethernet0/0
 ip address 172.24.0.5 255.255.255.252
 ip router isis
 ipv6 address 2001:DB8:ACAD:3::24:1/126
 ipv6 router isis
!
interface Ethernet0/1
 description TO-CLIENT-LABYTNANGI
 ip address 10.0.0.1 255.255.255.252
 ip router isis
!
interface Ethernet0/2
 ip address 172.24.0.1 255.255.255.252
 ip router isis
 ipv6 address 2001:DB8:ACAD:3::24:E/126
 ipv6 router isis
!
interface Ethernet0/3
 description TO-CLIENT-CHOK-1
 ip address 10.0.0.5 255.255.255.252
 ip router isis
!
router isis
 net 49.2222.0001.0001.0001.00
!
end
```  

**2. Настроил IS-IS на маршрутизаторе R24**  
Конфигурация маршрутизатора R24  
```
Current configuration : 1361 bytes
!
! Last configuration change at 15:37:01 +07 Thu Apr 29 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R24
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
interface Ethernet0/1
 ip address 172.24.0.10 255.255.255.252
 ip router isis
 ipv6 address 2001:DB8:ACAD:3::24:9/126
 ipv6 router isis
!
interface Ethernet0/2
 ip address 172.24.0.13 255.255.255.252
 ip router isis
 ipv6 address 2001:DB8:ACAD:3::24:6/126
 ipv6 router isis
!
router isis
 net 49.0024.0024.0024.0001.00
!
end
```  

**3. Настроил IS-IS на маршрутизаторе R26**  
Конфигурация маршрутизатора R26  
```
Current configuration : 1413 bytes
!
! Last configuration change at 15:26:30 +07 Thu Apr 29 2021
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
ipv6 unicast-routing
ipv6 cef
!
multilink bundle-name authenticated
!
interface Ethernet0/0
 ip address 172.24.0.9 255.255.255.252
 ip router isis
 ipv6 address 2001:DB8:ACAD:3::24:A/126
 ipv6 router isis
!
interface Ethernet0/1
 description TO-CLIENT-2
 ip address 10.0.1.5 255.255.255.252
 ip router isis
!
interface Ethernet0/2
 ip address 172.24.0.2 255.255.255.252
 ip router isis
 ipv6 address 2001:DB8:ACAD:3::24:D/126
 ipv6 router isis
!
router isis
 net 49.0026.0026.0026.0001.00
!
end
```  

**Соседство IS-IS IPv4 & IPv6 и таблицы маршрутизации IPv4 & IPv6 на маршрутизаторе R23**  
![](https://github.com/merkelev/neteng/blob/main/labs/7-IS-IS/R23-IS-IS.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/7-IS-IS/R23-ROUTING.png)  

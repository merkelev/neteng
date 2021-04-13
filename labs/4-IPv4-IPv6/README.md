### IPv4/IPv6

**Цель: Задокументировать и настроить адресное пространство сети в г. Москва и г. Санкт-Петербург**  

**Схема сети г. Москва**  
![](https://github.com/merkelev/neteng/blob/main/labs/4-IPv4-IPv6/NET-MOSCOW.png)
Добавил линки между R12 и SW5, R13 и SW4, SW3 и SW2. Линки добавил для избыточности когда будет динамическая маршрутизация в сети.    
Удалил один линк между SW4 и SW5. Заколцевал сеть между коммутаторами на L2.  

**Таблица адресов IPv4 г. Москва**  
| Device   | Interface     | IP Address | Subnet Mask | Default Gateway | Description  |
| -------- | ------------- | --------   | --------    | --------        |
| R14      | Et0/3         | 172.18.0.1  | 255.255.255.252 |            | to R19:et0/0 |
|          | Et0/0         | 172.18.0.5  | 255.255.255.252 |            | to R12:et0/2 |
|          | Et0/1         | 172.18.0.9  | 255.255.255.252 |            | to R13:et0/3 |
| R19      | Et0/0         | 172.18.0.2  | 255.255.255.252 | 172.18.0.1 | to R14:et0/3 |
| R15      | Et0/0         | 172.18.0.17 | 255.255.255.252 |            | to R13:et0/2 |
|          | Et0/1         | 172.18.0.13 | 255.255.255.252 |            | to R12:et0/3 |
|          | Et0/3         | 172.18.0.21 | 255.255.255.252 |            | to R20:et0/0 | 
| R20      | Et0/0         | 172.18.0.22 | 255.255.255.252 | 172.18.0.21| to R15:et0/3 |
| R12      | Et0/0         | 172.16.0.5  | 255.255.255.252 |            |
|          | Et0/1         | 172.16.0.13 | 255.255.255.252 |            |
|          | Et0/3         | 172.18.0.14 | 255.255.255.252 |            | to R15:et0/1 |
|          | Et0/2         | 172.18.0.6  | 255.255.255.252 |            | to R14:et0/0 |
| R13      | Et0/0         | 172.16.0.1  | 255.255.255.252 |            | 
|          | Et0/1         | 172.16.0.9  | 255.255.255.252 |            |
|          | Et0/3         | 172.18.0.10 | 255.255.255.252 |            | to R14:et0/1 |
|          | Et0/2         | 172.18.0.18 | 255.255.255.252 |            | to R15:et0/0 | 
| VPC1     | NIC           | 172.17.12.2 | 255.255.255.248 | 172.17.12.1|
| VPC7     | NIC           | 172.17.14.3 | 255.255.255.248 | 172.17.14.1|
| SW4      | VLAN9         | 172.17.9.10 | 255.255.255.240 | 172.17.9.1 |
|          | VLAN12        | 172.17.12.1 | 255.255.255.248 |            |
|          | Et1/0         | 172.16.0.6  | 255.255.255.252 | 172.16.0.5 |
|          | Et0/2         | 172.16.0.10 | 255.255.255.252 |            |
| SW5      | VLAN9         | 172.17.9.11 | 255.255.255.240 | 172.17.9.1 |
|          | VLAN14        | 172.17.14.1 | 255.255.255.248 |            |
|          | Et1/0         | 172.16.0.2  | 255.255.255.252 | 172.16.0.1 |
|          | Et0/2         | 172.16.0.14 | 255.255.255.252 |            |
| SW3      | VLAN9         | 172.17.9.12 | 255.255.255.240 | 172.17.9.1 |
| SW2      | VLAN9         | 172.17.9.13 | 255.255.255.240 | 172.17.9.1 |


**Таблица VLAN г. Москва**  
| VLAN | Name | Interface Assigned  |
| ---- | ----- | ------------------ |
| 66   | NATIVE| N/A                |
| 9    | MNGM  | SW4 VLAN9, SW5 VLAN9, SW3 VLAN9, SW2 VLAN9 |
| 12   | ACC   | SW3 Et0/2                 |
| 14   | SKLAD | SW2 Et0/2                 |
| 54   | PRK-LOT | SW4 Et1/1, Et1/2, Et1/3 |
|      |         | SW5 Et1/1, Et1/2, Et1/3 |
|      |         | SW3 Et1/0, Et1/1, Et1/2, Et1/3 |
|      |         | SW2 Et1/0, Et1/1, Et1/2, Et1/3 |

**Конфигурации коммутаторов:**  
**SW3**  
```
Current configuration : 1755 bytes
!
! Last configuration change at 16:07:20 +07 Sat Apr 10 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW3
!
boot-start-marker
boot-end-marker
!
!
!
no aaa new-model
clock timezone +07 7 0
!
!
!
!
!
vtp mode transparent
!
!
!
ip cef
no ipv6 cef
!
!
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
vlan 9
 name MNGM
!
vlan 12
 name ACC
!
vlan 14
 name SKLAD
!
vlan 54
 name PRK-LOT
!
interface Ethernet0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet0/2
 switchport access vlan 12
 switchport mode access
!
interface Ethernet0/3
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet1/0
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan9
 ip address 172.17.9.12 255.255.255.240
!
ip forward-protocol nd
```

**SW2**  
```
Current configuration : 1679 bytes
!
! Last configuration change at 16:06:31 +07 Sat Apr 10 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW2
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
vtp mode transparent
!
ip cef
no ipv6 cef
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
vlan 9,12,14
!
vlan 54
 name PRK-LOT
!
interface Ethernet0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet0/1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet0/2
 switchport access vlan 14
 switchport mode access
!
interface Ethernet0/3
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
!
interface Ethernet1/0
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan9
 ip address 172.17.9.13 255.255.255.240
!
ip forward-protocol nd
!
end
```

**SW4**  
```
Current configuration : 1950 bytes
!
! Last configuration change at 16:12:46 +07 Sat Apr 10 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW4
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
vtp mode transparent
!
no ip domain-lookup
ip cef
no ipv6 cef

!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
vlan 9
 name MNGM
!
vlan 12
 name ACC
!
vlan 14
 name SKLAD
!
vlan 54
 name PRK-LOT
!
interface Ethernet0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet0/1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet0/2
 description TO-R13-ET0/1
 no switchport
 ip address 172.16.0.10 255.255.255.252
 duplex auto
!
interface Ethernet0/3
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet1/0
 description TO-R12-ET0/0
 no switchport
 ip address 172.16.0.6 255.255.255.252
 duplex auto
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
 duplex auto
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan9
 ip address 172.17.9.10 255.255.255.240
!
interface Vlan12
 ip address 172.17.12.1 255.255.255.248
!
ip forward-protocol nd
!
ip route 0.0.0.0 0.0.0.0 Ethernet1/0 172.16.0.5
!
end

```

**SW5**  
```
Current configuration : 1887 bytes
!
! Last configuration change at 16:11:24 +07 Sat Apr 10 2021
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname SW5
!
boot-start-marker
boot-end-marker
!
no aaa new-model
clock timezone +07 7 0
!
vtp mode transparent
!
ip cef
no ipv6 cef
!
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
vlan 9,12,14
!
vlan 54
 name PRK-LOT
!
interface Ethernet0/0
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet0/1
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet0/2
 description TO-R12-ET0/1
 no switchport
 ip address 172.16.0.14 255.255.255.252
 duplex auto
!
interface Ethernet0/3
 switchport trunk allowed vlan 9,12,14
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 66
 switchport mode trunk
 duplex auto
!
interface Ethernet1/0
 description TO-R13-ET0/0
 no switchport
 ip address 172.16.0.2 255.255.255.252
 duplex auto
!
interface Ethernet1/1
 switchport access vlan 54
 switchport mode access
 shutdown
 duplex auto
!
interface Ethernet1/2
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Ethernet1/3
 switchport access vlan 54
 switchport mode access
 shutdown
!
interface Vlan9
 ip address 172.17.9.11 255.255.255.240
!
interface Vlan14
 ip address 172.17.14.1 255.255.255.248
!
ip forward-protocol nd
!
ip route 0.0.0.0 0.0.0.0 Ethernet1/0 172.16.0.1
!
end

```

**Конфигурации маршрутизаторов**  
**R12**  
```
Current configuration : 1196 bytes
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R12
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
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-SW4-ET1/0
 ip address 172.16.0.5 255.255.255.252
!
interface Ethernet0/1
 description TO-SW5-ET0/2
 ip address 172.16.0.13 255.255.255.252
!
interface Ethernet0/2
 description TO-R14-ET0/0
 ip address 172.18.0.13 255.255.255.252
!
interface Ethernet0/3
 description TO-R15-ET0/1
 ip address 172.18.0.9 255.255.255.252
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
end

```  
**Таблица маршрутизации на R12**  
```
Gateway of last resort is not set
      172.16.0.0/16 is variably subnetted, 4 subnets, 2 masks
C        172.16.0.4/30 is directly connected, Ethernet0/0
L        172.16.0.5/32 is directly connected, Ethernet0/0
C        172.16.0.12/30 is directly connected, Ethernet0/1
L        172.16.0.13/32 is directly connected, Ethernet0/1
      172.18.0.0/16 is variably subnetted, 4 subnets, 2 masks
C        172.18.0.8/30 is directly connected, Ethernet0/3
L        172.18.0.9/32 is directly connected, Ethernet0/3
C        172.18.0.12/30 is directly connected, Ethernet0/2
L        172.18.0.13/32 is directly connected, Ethernet0/2
```

**R13**  
```
Current configuration : 1194 bytes
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R13
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
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-SW5-ET1/0
 ip address 172.16.0.1 255.255.255.252
!
interface Ethernet0/1
 description TO-SW4-ET0/2
 ip address 172.16.0.9 255.255.255.252
!
interface Ethernet0/2
 description TO-R15-ET0/0
 ip address 172.18.0.2 255.255.255.252
!
interface Ethernet0/3
 description TO-R14-ET0/1
 ip address 172.18.0.5 255.255.255.252
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
end

```  
**Таблица маршрутизации на R13**  
```
Gateway of last resort is not set
      172.16.0.0/16 is variably subnetted, 4 subnets, 2 masks
C        172.16.0.0/30 is directly connected, Ethernet0/0
L        172.16.0.1/32 is directly connected, Ethernet0/0
C        172.16.0.8/30 is directly connected, Ethernet0/1
L        172.16.0.9/32 is directly connected, Ethernet0/1
      172.18.0.0/16 is variably subnetted, 4 subnets, 2 masks
C        172.18.0.0/30 is directly connected, Ethernet0/2
L        172.18.0.2/32 is directly connected, Ethernet0/2
C        172.18.0.4/30 is directly connected, Ethernet0/3
L        172.18.0.5/32 is directly connected, Ethernet0/3
```  

**R14**  
```
Current configuration : 1021 bytes
!
! Last configuration change at 15:17:49 +07 Sat Apr 10 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R14
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
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R12-ET0/2
 ip address 172.18.0.14 255.255.255.252
!
interface Ethernet0/1
 description TO-R13-ET0/3
 ip address 172.18.0.6 255.255.255.252
!
interface Ethernet0/2
 no ip address
 shutdown
!
interface Ethernet0/3
 description TO-R19-ET0/0
 ip address 172.19.0.1 255.255.255.252
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
!
control-plane
!
end

```  
**Таблица маршрутизации на R14**  
```
Gateway of last resort is not set
      172.18.0.0/16 is variably subnetted, 4 subnets, 2 masks
C        172.18.0.4/30 is directly connected, Ethernet0/1
L        172.18.0.6/32 is directly connected, Ethernet0/1
C        172.18.0.12/30 is directly connected, Ethernet0/0
L        172.18.0.14/32 is directly connected, Ethernet0/0
      172.19.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.19.0.0/30 is directly connected, Ethernet0/3
L        172.19.0.1/32 is directly connected, Ethernet0/3
```  
**R15**  
```
Current configuration : 1021 bytes
!
! Last configuration change at 15:16:25 +07 Sat Apr 10 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R15
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
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R13-ET0/2
 ip address 172.18.0.1 255.255.255.252
!
interface Ethernet0/1
 description TO-R12-ET0/3
 ip address 172.18.0.10 255.255.255.252
!
interface Ethernet0/2
 no ip address
 shutdown
!
interface Ethernet0/3
 description TO-R20-ET0/0
 ip address 172.19.0.5 255.255.255.252
!
ip forward-protocol nd
!
end

```  
**Таблица маршрутизации на R15**  
```
Gateway of last resort is not set
      172.18.0.0/16 is variably subnetted, 4 subnets, 2 masks
C        172.18.0.0/30 is directly connected, Ethernet0/0
L        172.18.0.1/32 is directly connected, Ethernet0/0
C        172.18.0.8/30 is directly connected, Ethernet0/1
L        172.18.0.10/32 is directly connected, Ethernet0/1
      172.19.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.19.0.4/30 is directly connected, Ethernet0/3
L        172.19.0.5/32 is directly connected, Ethernet0/3
```  

**R19**  
```
Current configuration : 976 bytes
!
! Last configuration change at 15:14:24 +07 Sat Apr 10 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R19
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
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R14-ET0/3
 ip address 172.19.0.2 255.255.255.252
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
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 172.19.0.1
!
end

```  

**Таблица маршрутизации на R19**  
```
Gateway of last resort is 172.19.0.1 to network 0.0.0.0
S*    0.0.0.0/0 [1/0] via 172.19.0.1
      172.19.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.19.0.0/30 is directly connected, Ethernet0/0
L        172.19.0.2/32 is directly connected, Ethernet0/0
```  

**R20**  
```
Current configuration : 976 bytes
!
! Last configuration change at 15:15:39 +07 Sat Apr 10 2021
!
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R20
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
ip cef
no ipv6 cef
!
multilink bundle-name authenticated
!
redundancy
!
interface Ethernet0/0
 description TO-R15-ET0/3
 ip address 172.19.0.6 255.255.255.252
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
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip route 0.0.0.0 0.0.0.0 172.19.0.5
!
end
```  

**Таблица маршрутизации на R20**  
```
Gateway of last resort is 172.19.0.5 to network 0.0.0.0
S*    0.0.0.0/0 [1/0] via 172.19.0.5
      172.19.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.19.0.4/30 is directly connected, Ethernet0/0
L        172.19.0.6/32 is directly connected, Ethernet0/0
```  


**Таблица адресов IPv4 г. Санкт-Петербург**  
| Device   | Interface     | IP Address | Subnet Mask | Default Gateway | 
| -------- | ------------- | --------   | --------    | --------        |
| VPC1     | NIC           | 172.17.12.2 | 255.255.255.248 | 172.17.12.1|
| VPC7     | NIC           | 172.17.14.3 | 255.255.255.248 | 172.17.14.1|
| SW4      | VLAN9         | 172.17.9.10 | 255.255.255.240 | 172.17.9.1 |
|          | VLAN12        | 172.17.12.1 | 255.255.255.248 |            |
|          | Et1/0         | 172.16.0.6  | 255.255.255.252 | 172.16.0.5 |
|          | Et0/2         | 172.16.0.10 | 255.255.255.252 |            |
| SW5      | VLAN9         | 172.17.9.11 | 255.255.255.240 | 172.17.9.1 |
|          | VLAN14        | 172.17.14.1 | 255.255.255.248 |            |
|          | Et1/0         | 172.16.0.2  | 255.255.255.252 | 172.16.0.1 |
|          | Et0/2         | 172.16.0.14 | 255.255.255.252 |            |
| SW3      | VLAN9         | 172.17.9.12 | 255.255.255.240 | 172.17.9.1 |
| SW2      | VLAN9         | 172.17.9.13 | 255.255.255.240 | 172.17.9.1 |
| R16      | Et0/1         | 192.168.18.2 | 255.255.255.252 |           |
|          | Et0/2         | 192.168.17.6 | 255.255.255.252 |           |
| R17      | Et0/2         | 192.168.17.2 | 255.255.255.252 |           |
|          | Et0/1         | 192.168.18.6 | 255.255.255.252 |           |
| R18      | Et0/0         | 192.168.18.1 | 255.255.255.252 |           |
|          | Et0/1         | 192.168.18.5 | 255.255.255.252 |           |    
| R32      | Et0/1         | 192.168.17.1 | 255.255.255.252 |           |
|          | Et0/0         | 192.168.17.5 | 255.255.255.252 |           |

**Таблица VLAN г. Санкт-Петербург**  
| VLAN | Name | Interface Assigned  |
| ---- | ----- | ------------------ |
| 77   | NATIVE| N/A                |
| 9    | MNGM  | SW4 VLAN9, SW5 VLAN9, SW3 VLAN9, SW2 VLAN9|
| 12   |    |                    |
| 14   |  |                    |
| 54   | PRK-LOT |                  |


### Basic Internet protocols
**Схема сети**  
![](https://github.com/merkelev/neteng/blob/main/labs/12-Basic%20Internet%20protocols/images/2021-06-22_10-59-55.png)  

**Цель:**
1. Настроить NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.  
2. Настроить NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.  
3. Настроить статический NAT для R20.  
4. Настроить NAT так, чтобы R19 был доступен с любого узла для удаленного управления.  
5. Настроить статический NAT(PAT) для офиса Чокурдах.  
6. Настроить DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.  
7. Настроить NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.  

**Выполнение**  
**1. Настроить NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.**  
Настройка NAT на R14  
```
ip nat pool POOL-NAT-12 109.226.218.40 109.226.218.40 prefix-length 24
ip nat pool POOL-NAT-14 109.226.218.50 109.226.218.50 prefix-length 24
ip nat inside source list ACL-NAT-NETWORK-12 pool POOL-NAT-12 overload
ip nat inside source list ACL-NAT-NETWORK-14 pool POOL-NAT-14 overload
ip nat inside source static 172.18.0.62 109.226.218.20 extendable
ip nat inside source static tcp 172.18.0.2 22 109.226.219.22 4222 extendable
```  
ACL для NAT  
```
Extended IP access list ACL-NAT-NETWORK-12
    10 permit ip 172.18.12.0 0.0.0.255 any
Extended IP access list ACL-NAT-NETWORK-14
    10 permit ip 172.18.14.0 0.0.0.255 any
```  
НАТятся сети 172.18.12.0/25 и 172.18.14.0/25  

Настройка NAT на R15  
```
ip nat pool POOL-NAT-12 109.226.218.40 109.226.218.40 prefix-length 24
ip nat pool POOL-NAT-14 109.226.218.50 109.226.218.50 prefix-length 24
ip nat inside source list ACL-NAT-NETWORK-12 pool POOL-NAT-12 overload
ip nat inside source list ACL-NAT-NETWORK-14 pool POOL-NAT-14 overload
ip nat inside source static 172.18.0.22 109.226.218.20 extendable
ip nat inside source static tcp 172.18.0.66 22 109.226.219.22 4222 extendable
```  
ACL для NAT  
```
Extended IP access list ACL-NAT-NETWORK-12
    10 permit ip 172.18.12.0 0.0.0.255 any
Extended IP access list ACL-NAT-NETWORK-14
    10 permit ip 172.18.14.0 0.0.0.255 any
```  
НАТятся сети 172.18.12.0/25 и 172.18.14.0/25

Проверяем работу NAT для хостов VPC1 & VPC7, запускаю ping до узлов 95.188.120.2 & 95.188.50.2   
![](https://github.com/merkelev/neteng/blob/main/labs/12-Basic%20Internet%20protocols/images/NAT-TRANS-R15.png)  

Трафик идет через R15, в случае отказа R15, маршрутизатор R14 - является резервным. Отключаю маршрутизатор R15 и проверяю NAT  
![](https://github.com/merkelev/neteng/blob/main/labs/12-Basic%20Internet%20protocols/images/NAT-TRANS-R14.png)  

**2. Настроить NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.**  
Настройка NAT на R18  
```
ip nat pool POOL-NAT-18 75.100.20.20 75.100.20.25 prefix-length 24
ip nat inside source list ACL-NAT-18 pool POOL-NAT-18
```  
ACL для NAT  
```
Extended IP access list ACL-NAT-18
    10 permit ip 172.22.22.0 0.0.0.127 any
    20 permit ip 172.22.24.0 0.0.0.127 any
```  
НАТятся сети 172.22.22.0/25 и 172.22.24.0/25  
Проверяем работу NAT для хостов VPC & VPC8, запускаю ping до узлов 95.188.120.2 & 95.188.50.2   
![](https://github.com/merkelev/neteng/blob/main/labs/12-Basic%20Internet%20protocols/images/NAT-TRANS-R18.png)  
NAT работает. Трансляция осуществляется в пул пяти адресов AS 2041 - 75.100.20.20 75.100.20.25  

**3. Настроить статический NAT для R20.**  
Настройка на R14  
```
ip nat inside source static 172.18.0.62 109.226.218.20 extendable
```  

Настройка на R15  
```
ip nat inside source static 172.18.0.22 109.226.218.20 extendable
```  
В случае отказа R15, R14 будет НАТить R20. Прверяю работу NAT, запускаю ping до узла 95.188.100.2
![](https://github.com/merkelev/neteng/blob/main/labs/12-Basic%20Internet%20protocols/images/NAT-R20.png)  

**4. Настроить NAT так, чтобы R19 был доступен с любого узла для удаленного управления.**  
Настройка на R14  
```
ip nat inside source static tcp 172.18.0.2 22 109.226.219.22 4222 extendable
```  

Настройка на R15  
```
ip nat inside source static tcp 172.18.0.66 22 109.226.219.22 4222 extendable
```  
В случае отказа R15, R14 будет НАТить R19. Прверяю работу NAT и доступность управления по SSH с R27 и R28  
![](https://github.com/merkelev/neteng/blob/main/labs/12-Basic%20Internet%20protocols/images/ACCESS-R19.png)  

**5. Настроить статический NAT(PAT) для офиса Чокурдах.**  
Настраивал ранее в лабе по PBR. Настройка на R28  
```
ip nat translation timeout 5
ip nat inside source route-map RM-ISP-1 interface Ethernet0/0 overload
ip nat inside source route-map RM-ISP-2 interface Ethernet0/1 overload
ip route 0.0.0.0 0.0.0.0 95.188.50.1 track 1
ip route 0.0.0.0 0.0.0.0 95.188.100.1 10 track 2
!
ip access-list extended ACL-NAT-32
 permit ip 172.20.32.0 0.0.0.7 any
ip access-list extended ACL-NAT-34
 permit ip 172.20.34.0 0.0.0.7 any
!
ip sla 1
 icmp-echo 95.188.50.1 source-ip 95.188.50.2
 frequency 30
ip sla schedule 1 life forever start-time now
ip sla 2
 icmp-echo 95.188.100.1 source-ip 95.188.100.2
 frequency 30
ip sla schedule 2 life forever start-time now
!
route-map PBR permit 10
 match ip address ACL-NAT-32
 set ip next-hop verify-availability 95.188.50.1 1 track 1
 set ip next-hop verify-availability 95.188.100.1 2 track 2
!
route-map PBR permit 20
 match ip address ACL-NAT-34
 set ip next-hop verify-availability 95.188.100.1 1 track 2
 set ip next-hop verify-availability 95.188.50.1 2 track 1
!
route-map RM-ISP-1 permit 10
 match ip address ACL-NAT-32 ACL-NAT-34
 match interface Ethernet0/0
!
route-map RM-ISP-2 permit 10
 match ip address ACL-NAT-34 ACL-NAT-32
 match interface Ethernet0/1
!
```  
Проверяем работу NAT для хостов VPC30 & VPC31, запускаю ping до узла 95.188.120.2
![](https://github.com/merkelev/neteng/blob/main/labs/12-Basic%20Internet%20protocols/images/NAT-TRANS-R28.png)  

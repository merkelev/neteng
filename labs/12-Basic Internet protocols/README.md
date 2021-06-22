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
**Настроить NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.**  
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

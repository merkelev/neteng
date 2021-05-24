### BGP. BASES  

**Цель: Настроить BGP между автономными системами Организовать доступность между офисами Москва и С.-Петербург**  
**1. Настроить eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас**  
**2. Настроить eBGP между провайдерами Киторн и Ламас**  
**3. Настроить eBGP между Ламас и Триада**  
**4. Настроить eBGP между офисом С.-Петербург и провайдером Триада**  
**5. Организовать IP доступность между офисами Москва и С.-Петербург**  

**Таблица IPv4 адресов для стыков**  
| Device | Interface | IPv4 address | Mask | Description  |
| ------ | --------- | ------------ | ---- | ------------ |
| R14    | et0/2     | 10.0.2.2     | /30  | to R22 et0/0 |
| R15    | et0/2     | 10.0.2.6     | /30  | to R21 et0/0 |
| R22    | et0/0     | 10.0.2.1     | /30  | to R14 et0/2 |
|        | et0/1     | 10.0.2.10    | /30  | to R21 et0/1 |
|        | et0/2     | 10.0.7.1     | /30  | to R23 et0/0 |
| R21    | et0/0     | 10.0.2.5     | /30  | to R15 et0/2 |
|        | et0/1     | 10.0.2.9     | /30  | to R22 et0/1 |
|        | et0/2     | 10.0.6.2     | /30  | to R24 et0/0 |
| R23    | et0/0     | 10.0.7.2     | /30  | to R22 et0/2 |
| R24    | et0/0     | 10.0.6.1     | /30  | to R21 et0/2 |
|        | et0/3     | 10.0.4.1     | /30  | to R18 et0/2 |
| R26    | et0/3     | 10.0.5.1     | /30  | to R18 et0/3 |
| R18    | et0/2     | 10.0.4.2     | /30  | to R24 et0/3 |
|        | et0/3     | 10.0.5.2     | /30  | to R26 et0/3 |

**Таблица IPv6 адресов для стыков**  
| Device | Interface | IPv6 address           | Prefix     | Description  |
| ------ | --------- | ---------------------- | ---------- | ------------ |
| R14    | et0/2     | 2001:AD7:ACAB:1::1/126 | /126       | to R22 et0/0 |
|        |           | FE80::6:1              | link-local |              |
| R15    | et0/2     | 2001:db7:acab:3::2     | /126       | to R21 et0/0 |
|        |           | FE80::8:2              | link-local |              |
| R22    | et0/0     | 2001:db7:acab:1::2     | /126       | to R14 et0/2 |
|        |           | FE80::6:2              | link-local |              |
|        | et0/1     | 2001:db7:acab:2::1     | /126       | to R21 et0/1 |
|        |           | FE80::7:1              | link-local |              |
|        | et0/2     | 2001:db7:acab:4::1     | /126       | to R23 et0/0 |
|        |           | FE80::4:1              | link-local |              |
| R21    | et0/0     | 2001:db7:acab:3::1     | /126       | to R15 et0/2 |
|        |           | FE80::8:1              | link-local |              |
|        | et0/1     | 2001:db7:acab:2::2     | /126       | to R22 et0/1 |
|        |           | FE80::7:2              | link-local |              |
|        | et0/2     | 2001:db7:acab:5::1     | /126       | to R24 et0/0 |
|        |           | FE80::5:1              | link-local |              |
| R23    | et0/0     | 2001:db7:acab:4::2     | /126       | to R22 et0/2 |
|        |           | FE80::4::2             | link-local |              |
| R24    | et0/0     | 2001:db7:acab:5::2     | /126       | to R21 et0/2 |
|        |           | FE80::5:2              | link-local |              |
|        | et0/3     | 2001:DB8:ACAD:5::2     | /126       | to R18 et0/2 |
|        |           | FE80::5:2              | link-local |              |
| R26    | et0/3     | 2001:DB8:ACAD:6::2     | /126       | to R18 et0/3 |
|        |           | FE80::6:2              | link-local |              |
| R18    | et0/2     | 2001:DB8:ACAD:5::1     | /126       | to R24 et0/3 |
|        |           | FE80::2:1              | link-local |              |
|        | et0/3     | 2001:DB8:ACAD:6::1     | /126       | to R26 et0/3 |
|        |           | FE80::6:1              | link-local |              |

**1. Настроика eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас**  
**Схема сети**  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/NET-MSK-KRITON-LAMAS.png)  
Настройки BGP маршрутизатора R14 (г. Москва)  
```
router bgp 1001
 bgp log-neighbor-changes
 neighbor 10.0.2.1 remote-as 101
 neighbor 2001:DB7:ACAB:1::2 remote-as 101
 !
 address-family ipv4
  network 172.18.12.0 mask 255.255.255.128
  network 172.18.14.0 mask 255.255.255.128
  neighbor 10.0.2.1 activate
  no neighbor 2001:DB7:ACAB:1::2 activate
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:2::12:0/120
  network 2001:DB8:ACAD:2::14:0/120
  neighbor 2001:DB7:ACAB:1::2 activate
 exit-address-family
!
```  

Настройки BGP маршрутизатора R15 (г. Москва)  
```
router bgp 1001
 bgp log-neighbor-changes
 neighbor 10.0.2.5 remote-as 301
 neighbor 2001:DB7:ACAB:3::1 remote-as 301
 !
 address-family ipv4
  network 172.18.12.0 mask 255.255.255.128
  network 172.18.14.0 mask 255.255.255.128
  neighbor 10.0.2.5 activate
  no neighbor 2001:DB7:ACAB:3::1 activate
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:2::12:0/120
  network 2001:DB8:ACAD:2::14:0/120
  neighbor 2001:DB7:ACAB:3::1 activate
 exit-address-family
!
```  

Настройки BGP маршрутизатора R22 (Критон)  
```
router bgp 101
 bgp log-neighbor-changes
 neighbor 10.0.2.2 remote-as 1001
 neighbor 10.0.2.9 remote-as 301
 neighbor 10.0.7.2 remote-as 520
 neighbor 2001:DB7:ACAB:1::1 remote-as 1001
 neighbor 2001:DB7:ACAB:2::2 remote-as 301
 neighbor 2001:DB7:ACAB:4::2 remote-as 520
 !
 address-family ipv4
  network 172.30.0.0 mask 255.255.255.0
  neighbor 10.0.2.2 activate
  neighbor 10.0.2.9 activate
  neighbor 10.0.7.2 activate
  no neighbor 2001:DB7:ACAB:1::1 activate
  no neighbor 2001:DB7:ACAB:2::2 activate
  no neighbor 2001:DB7:ACAB:4::2 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:1::1 activate
  neighbor 2001:DB7:ACAB:2::2 activate
  neighbor 2001:DB7:ACAB:4::2 activate
 exit-address-family
!
```  

Настройки BGP маршрутизатора R21 (Ламас)  
```
router bgp 301
 bgp log-neighbor-changes
 neighbor 10.0.2.6 remote-as 1001
 neighbor 10.0.2.10 remote-as 101
 neighbor 10.0.6.1 remote-as 520
 neighbor 2001:DB7:ACAB:2::1 remote-as 101
 neighbor 2001:DB7:ACAB:3::2 remote-as 1001
 neighbor 2001:DB7:ACAB:5::2 remote-as 520
 !
 address-family ipv4
  neighbor 10.0.2.6 activate
  neighbor 10.0.2.10 activate
  neighbor 10.0.6.1 activate
  no neighbor 2001:DB7:ACAB:2::1 activate
  no neighbor 2001:DB7:ACAB:3::2 activate
  no neighbor 2001:DB7:ACAB:5::2 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:2::1 activate
  neighbor 2001:DB7:ACAB:3::2 activate
  neighbor 2001:DB7:ACAB:5::2 activate
 exit-address-family
!
```  

Проверим состояние соседства R14 (г. Москва) & R22 (Критон)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R14-BGP-R22.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R14-BGPIPv6-R22.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R22-BGP-R14.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R22-BGPIPv6-R14.png)  

**2. Настройка eBGP между провайдерами Киторн и Ламас**  
В пункте 1 есть настройки eBGP между R22 & R21  
Проверим состояние соседства R22 (Критон) & R21 (Ламас)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R21-BGP-R22.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R21-BGPIPv6-R22.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R22-BGP-R21.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R22-BGPIPv6-R21.png)  

**3. Настройка eBGP между Ламас, Критон и Триада**  
**Схема сети**  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/NET-KRITON-LAMAS-TRIADA.png)  
Настройки BGP маршрутизатора R23 (Триада)  
```
router bgp 520
 bgp log-neighbor-changes
 neighbor 10.0.7.1 remote-as 101
 neighbor 2001:DB7:ACAB:4::1 remote-as 101
 !
 address-family ipv4
  neighbor 10.0.7.1 activate
  no neighbor 2001:DB7:ACAB:4::1 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:4::1 activate
 exit-address-family
!
```  
Настройки BGP маршрутизатора R22 (Критон)  
```
router bgp 101
 bgp log-neighbor-changes
 neighbor 10.0.2.2 remote-as 1001
 neighbor 10.0.2.9 remote-as 301
 neighbor 10.0.7.2 remote-as 520
 neighbor 2001:DB7:ACAB:1::1 remote-as 1001
 neighbor 2001:DB7:ACAB:2::2 remote-as 301
 neighbor 2001:DB7:ACAB:4::2 remote-as 520
 !
 address-family ipv4
  network 172.30.0.0 mask 255.255.255.0
  neighbor 10.0.2.2 activate
  neighbor 10.0.2.9 activate
  neighbor 10.0.7.2 activate
  no neighbor 2001:DB7:ACAB:1::1 activate
  no neighbor 2001:DB7:ACAB:2::2 activate
  no neighbor 2001:DB7:ACAB:4::2 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:1::1 activate
  neighbor 2001:DB7:ACAB:2::2 activate
  neighbor 2001:DB7:ACAB:4::2 activate
 exit-address-family
!
```  

Проверяем состояние соседства  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R23-BGP-R22.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R23-BGPIPv6-R22.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R22-BGP-R23.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R22-BGPIPv6-R23.png)  

Настройки BGP маршрутизатора R24 (Триада)  
```
router bgp 520
 bgp log-neighbor-changes
 neighbor 10.0.4.2 remote-as 2042
 neighbor 10.0.6.2 remote-as 301
 neighbor 2001:DB7:ACAB:5::1 remote-as 301
 neighbor 2001:DB8:ACAD:5::1 remote-as 2042
 !
 address-family ipv4
  neighbor 10.0.4.2 activate
  neighbor 10.0.6.2 activate
  no neighbor 2001:DB7:ACAB:5::1 activate
  no neighbor 2001:DB8:ACAD:5::1 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:5::1 activate
  neighbor 2001:DB8:ACAD:5::1 activate
 exit-address-family
!
```  

Настройки BGP маршрутизатора R21 (Ламас)  
```
router bgp 301
 bgp log-neighbor-changes
 neighbor 10.0.2.6 remote-as 1001
 neighbor 10.0.2.10 remote-as 101
 neighbor 10.0.6.1 remote-as 520
 neighbor 2001:DB7:ACAB:2::1 remote-as 101
 neighbor 2001:DB7:ACAB:3::2 remote-as 1001
 neighbor 2001:DB7:ACAB:5::2 remote-as 520
 !
 address-family ipv4
  neighbor 10.0.2.6 activate
  neighbor 10.0.2.10 activate
  neighbor 10.0.6.1 activate
  no neighbor 2001:DB7:ACAB:2::1 activate
  no neighbor 2001:DB7:ACAB:3::2 activate
  no neighbor 2001:DB7:ACAB:5::2 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:2::1 activate
  neighbor 2001:DB7:ACAB:3::2 activate
  neighbor 2001:DB7:ACAB:5::2 activate
 exit-address-family
!
```  
Проверяем состояние соседства  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R24-BGP-R21.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R24-BGPIPv6-R21.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R21-BGP-R24.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R21-BGPIPv6-R24.png)  

**4. Настроика eBGP между офисом С.-Петербург и провайдером Триада**  
**Схема сети**  
 ![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/NET-SP-TRIADA.png)  
 
 Настройки BGP маршрутизатора R24 (Триада)  
 ```
 router bgp 520
 bgp log-neighbor-changes
 neighbor 10.0.4.2 remote-as 2042
 neighbor 10.0.6.2 remote-as 301
 neighbor 2001:DB7:ACAB:5::1 remote-as 301
 neighbor 2001:DB8:ACAD:5::1 remote-as 2042
 !
 address-family ipv4
  neighbor 10.0.4.2 activate
  neighbor 10.0.6.2 activate
  no neighbor 2001:DB7:ACAB:5::1 activate
  no neighbor 2001:DB8:ACAD:5::1 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:5::1 activate
  neighbor 2001:DB8:ACAD:5::1 activate
 exit-address-family
!
 ```  
 
 Настройки BGP маршрутизатора R26 (Триада)  
 ```
 router bgp 520
 bgp log-neighbor-changes
 neighbor 10.0.5.2 remote-as 2042
 neighbor 2001:DB8:ACAD:6::1 remote-as 2042
 !
 address-family ipv4
  neighbor 10.0.5.2 activate
  no neighbor 2001:DB8:ACAD:6::1 activate
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB8:ACAD:6::1 activate
 exit-address-family
!
 ```  
 
 Настройки BGP маршрутизота R18 (Санкт-Петербург)  
 ```
 router bgp 2042
 bgp log-neighbor-changes
 neighbor 10.0.4.1 remote-as 520
 neighbor 10.0.5.1 remote-as 520
 neighbor 2001:DB8:ACAD:5::2 remote-as 520
 neighbor 2001:DB8:ACAD:6::2 remote-as 520
 !
 address-family ipv4
  network 172.22.0.0
  neighbor 10.0.4.1 activate
  neighbor 10.0.5.1 activate
  no neighbor 2001:DB8:ACAD:5::2 activate
  no neighbor 2001:DB8:ACAD:6::2 activate
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:1::/64
  neighbor 2001:DB8:ACAD:5::2 activate
  neighbor 2001:DB8:ACAD:6::2 activate
 exit-address-family
!
 ```  
 
 Проверяем состояние соседства  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R18-BGP-R24.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R18-BGP-R26.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R18-BGPIPv6-R24.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R18-BGPIPv6-R26.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R24-BGP-R18.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R24-BGPIPv6-R18.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R26-BGP-R18.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R26-BGPIPv6-R18.png)  
 
**5. Организация IP доступности между офисами Москва и С.-Петербург**  
На маршрутизаторах в офисе г. Москва, на маршрутизаторах R14 & R15 добавил в BGP сети IPv4 - 172.18.12.0/25 и 172.18.14.0/25 & IPv6 - 2001:DB8:ACAD:2::12:0/120 и 2001:DB8:ACAD:2::14:0/120. Это сети которые назанчаются только клиентским машинам.  
На R14  
```
 address-family ipv4
  network 172.18.12.0 mask 255.255.255.128
  network 172.18.14.0 mask 255.255.255.128
  neighbor 10.0.2.1 activate
  no neighbor 2001:DB7:ACAB:1::2 activate
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:2::12:0/120
  network 2001:DB8:ACAD:2::14:0/120
  neighbor 2001:DB7:ACAB:1::2 activate
 exit-address-family
!
```  
На R15  
```
address-family ipv4
  network 172.18.12.0 mask 255.255.255.128
  network 172.18.14.0 mask 255.255.255.128
  neighbor 10.0.2.5 activate
  no neighbor 2001:DB7:ACAB:3::1 activate
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:2::12:0/120
  network 2001:DB8:ACAD:2::14:0/120
  neighbor 2001:DB7:ACAB:3::1 activate
 exit-address-family
!
```  

Проверяем анонсы сетей IPv4 & IPv6 в BGP  
На R14  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R14-BGP-4-6.png)  

На R15  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R15-BGP-4-6.png)  
Видим что сети анонсируются.

На маршрутизаторе R18 в офисе г. Санкт-Петербург добавил в BGP сеть IPv4 - 172.22.0.0/16 & сеть IPv6 - 2001:DB8:ACAD:1::/64.  
```
 address-family ipv4
  network 172.22.0.0
  neighbor 10.0.4.1 activate
  neighbor 10.0.5.1 activate
  no neighbor 2001:DB8:ACAD:5::2 activate
  no neighbor 2001:DB8:ACAD:6::2 activate
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:1::/64
  neighbor 2001:DB8:ACAD:5::2 activate
  neighbor 2001:DB8:ACAD:6::2 activate
 exit-address-family
!
```  
Так же на R18 настроил редистрибуцию маршрутов из BGP в EIGRP.  
```
router eigrp SP-EIGRP
 !
 address-family ipv4 unicast autonomous-system 4
  !
  topology base
   redistribute static
   redistribute bgp 2042 metric 10000000 30 255 20 1400
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
   redistribute bgp 2042 metric 10000000 30 255 20 1400
  exit-af-topology
  eigrp router-id 172.22.44.1
 exit-address-family
!
```  

Проверяем анонсы сетей IPv4 & IPv6 в BGP
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/R18-BGP-4-6.png)  
Видим что сеть анонсируется.

Проверяем доступность с ПК VPC7 (г. Москва) до VPC8 (г. Санкт-Петербург)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/PING-VPC7-TO-VPC8.png)  

Проверяем доступность с ПК VPC (г. Санкт-Петербург) до VPC1 (г. Москва)  
![](https://github.com/merkelev/neteng/blob/main/labs/9-BGP-BASES/PING-VPC-TO-VPC1.png)  

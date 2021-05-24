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
Конфигурация BGP маршрутизатора R14 (г. Москва)  
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

Конфигурация BGP маршрутизатора R15 (г. Москва)  
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

Конфигурация BGP маршрутизатора R22 (Критон)  
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

Конфигурация BGP маршрутизатора R21 (Ламас)  
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
Настройки BGP маршрутизатора R23 (Триада)  
```

```  

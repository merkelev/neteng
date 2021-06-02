### iBGP
**Цель: Настроить iBGP в офисе Москва; Настроить iBGP в сети провайдера Триада; Организовать полную IP связанность всех сетей.**  
**1. Настроить iBGP в офисе Москва между маршрутизаторами R14 и R15;**  
**2. Настроить iBGP в провайдере Триада;**  
**3. Настройть офис Москва так, чтобы приоритетным провайдером стал Ламас;**  
**4. Настройть офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно;**  
**Все сети в лабораторной работе должны иметь IP связность;**  

**Схема сети:**  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/NET.png)  

**Таблица IPv4 адресов для стыков**  
| Device | Interface | IPv4 address | Mask | Description  |
| ------ | --------- | ------------ | ---- | ------------ |
| R14    | et0/2     | 95.188.0.2   | /30  | to R22 et0/0 |
| R15    | et0/2     | 95.188.1.2   | /30  | to R21 et0/0 |
| R22    | et0/0     | 95.188.0.1   | /30  | to R14 et0/2 |
|        | et0/1     | 95.188.2.2   | /30  | to R21 et0/1 |
|        | et0/2     | 95.188.20.2  | /30  | to R23 et0/0 |
| R21    | et0/0     | 95.188.1.1   | /30  | to R15 et0/2 |
|        | et0/1     | 95.188.2.1   | /30  | to R22 et0/1 |
|        | et0/2     | 95.188.10.2  | /30  | to R24 et0/0 |
| R23    | et0/0     | 95.188.20.1  | /30  | to R22 et0/2 |
| R24    | et0/0     | 95.188.10.1  | /30  | to R21 et0/2 |
|        | et0/3     | 95.188.30.1  | /30  | to R18 et0/2 |
| R26    | et0/3     | 95.188.40.1  | /30  | to R18 et0/3 |
|        | et0/1     | 95.188.50.1  | /30  | to R28 et0/0 |
| R18    | et0/2     | 95.188.30.2  | /30  | to R24 et0/3 |
|        | et0/3     | 95.188.40.2  | /30  | to R26 et0/3 |
| R25    | et0/1     | 95.188.120.1 | /30  | to R27 et0/0 |
|        | et0/3     | 95.188.100.1 | /30  | to R28 et0/1 |
| R27    | et0/0     | 95.188.120.2 | /30  | to R25 et0/1 |
| R28    | et0/0     | 95.188.50.2  | /30  | to R26 et0/1 |
|        | et0/1     | 95.188.100.2 | /30  | to R25 et0/3 |

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

**Таблица AS**  
| AS   | Location   | Device | Interface | Description |
| ---- | ---------  | ------ | --------- | ----------- |
| 1001 | г. Москва  | R14    | et0/2     | Стык с ISP Киторн R22  |
|      |            | R15    | et0/2     | Стык с ISP Ламас  R21  |
| 101  | ISP Киторн | R22    | et0/0     | Стык с г. Москва R14   |
|      |            |        | et0/1     | Стык с ISP Ламас R21   |
|      |            |        | et0/2     | Стык с ISP Триада R23  |
| 301  | ISP Ламас  | R21    | et0/0     | Стык с г. Москва R15   |
|      |            |        | et0/1     | Стык с ISP Киторн R22  |
|      |            |        | et0/2     | Стык с ISP Триада R24  |
| 520  | ISP Триада | R23    | et0/0     | Стык с ISP Киторн R22  |
|      |            | R24    | et0/0     | Стык с ISP Ламас R21   |
|      |            |        | et0/3     | Стык с г С.Петербург R18 |
|      |            | R26    | et0/3     | Стык с г С.Петербург R18 |
| 2042 | г. С.Петербург | R18| et0/2     | Стык с ISP Триада R24  |
|      |                |    | et0/3     | Стык с ISP Триада R26  |


**1. Настройка iBGP в офисе Москва между маршрутизаторами R14 и R15**  
**Таблица iBGP в С.-Петербург.**  
| Device | Interface | Address                  |
| ------ | --------- | ------------------------ |
| R14    | Loopback0 | 172.18.10.14             |
|        |           | 2001:DB8:ACAD:2::1001:14 |
| R15    | Loopback0 | 172.18.10.15             |
|        |           | 2001:DB8:ACAD:2::1001:15 |

Настройка iBGP на R14  
```
router bgp 1001
 bgp log-neighbor-changes
 neighbor 2001:DB7:ACAB:1::2 remote-as 101
 neighbor 2001:DB8:ACAD:2::1001:15 remote-as 1001
 neighbor 2001:DB8:ACAD:2::1001:15 update-source Loopback0
 neighbor 95.188.0.1 remote-as 101
 neighbor 172.18.10.15 remote-as 1001
 neighbor 172.18.10.15 update-source Loopback0
 !
 address-family ipv4
  network 172.18.12.0 mask 255.255.255.128
  network 172.18.14.0 mask 255.255.255.128
  no neighbor 2001:DB7:ACAB:1::2 activate
  no neighbor 2001:DB8:ACAD:2::1001:15 activate
  neighbor 95.188.0.1 activate
  neighbor 172.18.10.15 activate
  neighbor 172.18.10.15 next-hop-self
  neighbor 172.18.10.15 route-map LOCAL-PREF in
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:2::12:0/120
  network 2001:DB8:ACAD:2::14:0/120
  neighbor 2001:DB7:ACAB:1::2 activate
  neighbor 2001:DB8:ACAD:2::1001:15 activate
  neighbor 2001:DB8:ACAD:2::1001:15 next-hop-self
 exit-address-family
!
```  

Настройка iBGP на R15  
```
router bgp 1001
 bgp log-neighbor-changes
 neighbor 2001:DB7:ACAB:3::1 remote-as 301
 neighbor 2001:DB8:ACAD:2::1001:14 remote-as 1001
 neighbor 95.188.1.1 remote-as 301
 neighbor 172.18.10.14 remote-as 1001
 neighbor 172.18.10.14 update-source Loopback0
 !
 address-family ipv4
  network 172.18.12.0 mask 255.255.255.128
  network 172.18.14.0 mask 255.255.255.128
  no neighbor 2001:DB7:ACAB:3::1 activate
  no neighbor 2001:DB8:ACAD:2::1001:14 activate
  neighbor 95.188.1.1 activate
  neighbor 172.18.10.14 activate
  neighbor 172.18.10.14 next-hop-self
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:2::12:0/120
  network 2001:DB8:ACAD:2::14:0/120
  neighbor 2001:DB7:ACAB:3::1 activate
  neighbor 2001:DB8:ACAD:2::1001:14 activate
  neighbor 2001:DB8:ACAD:2::1001:14 next-hop-self
 exit-address-family
!
```  

**Проверяем соседство на R14 & R15**  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/R14-iBGP.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/R15-iBGP.png)  

**2. Настройка iBGP в провайдере Триада.**  
**Таблица iBGP ISP Триада.**  
| Device | Interface | Address                  |
| ------ | --------- | ------------------------ |
| R23    | Loopback0 | 172.24.52.23             |
|        |           | 2001:db8:acad:520::23    |
| R24    | Loopback0 | 172.24.52.24             |
|        |           | 2001:db8:acad:520::24    |
| R25    | Loopback0 | 172.24.52.25             |
|        |           | 2001:db8:acad:520::25    |
| R26    | Loopback0 | 172.24.52.26             |
|        |           | 2001:db8:acad:520::26    |

Настройки iBGP на R23  
```
router bgp 520
 bgp log-neighbor-changes
 neighbor 2001:DB7:ACAB:4::1 remote-as 101
 neighbor 2001:DB8:ACAD:520::24 remote-as 520
 neighbor 2001:DB8:ACAD:520::24 update-source Loopback0
 neighbor 2001:DB8:ACAD:520::25 remote-as 520
 neighbor 2001:DB8:ACAD:520::25 update-source Loopback0
 neighbor 95.188.20.2 remote-as 101
 neighbor 172.24.52.24 remote-as 520
 neighbor 172.24.52.24 update-source Loopback0
 neighbor 172.24.52.25 remote-as 520
 neighbor 172.24.52.25 update-source Loopback0
 !
 address-family ipv4
  redistribute isis as520 level-1-2 route-map RM-REDIST-ISIS
  no neighbor 2001:DB7:ACAB:4::1 activate
  no neighbor 2001:DB8:ACAD:520::24 activate
  no neighbor 2001:DB8:ACAD:520::25 activate
  neighbor 95.188.20.2 activate
  neighbor 172.24.52.24 activate
  neighbor 172.24.52.24 route-reflector-client
  neighbor 172.24.52.24 next-hop-self
  neighbor 172.24.52.25 activate
  neighbor 172.24.52.25 route-reflector-client
  neighbor 172.24.52.25 next-hop-self
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:4::1 activate
  neighbor 2001:DB8:ACAD:520::24 activate
  neighbor 2001:DB8:ACAD:520::24 next-hop-self
  neighbor 2001:DB8:ACAD:520::25 activate
  neighbor 2001:DB8:ACAD:520::25 next-hop-self
 exit-address-family
!
```  

Настройки iBGP на R24  
```
router bgp 520
 bgp log-neighbor-changes
 neighbor 2001:DB7:ACAB:5::1 remote-as 301
 neighbor 2001:DB8:ACAD:5::1 remote-as 2042
 neighbor 2001:DB8:ACAD:520::23 remote-as 520
 neighbor 2001:DB8:ACAD:520::23 update-source Loopback0
 neighbor 2001:DB8:ACAD:520::26 remote-as 520
 neighbor 2001:DB8:ACAD:520::26 update-source Loopback0
 neighbor 95.188.10.2 remote-as 301
 neighbor 95.188.30.2 remote-as 2042
 neighbor 172.24.52.23 remote-as 520
 neighbor 172.24.52.23 update-source Loopback0
 neighbor 172.24.52.26 remote-as 520
 neighbor 172.24.52.26 update-source Loopback0
 !
 address-family ipv4
  redistribute isis as520 level-1-2 route-map RM-REDIST-ISIS
  no neighbor 2001:DB7:ACAB:5::1 activate
  no neighbor 2001:DB8:ACAD:5::1 activate
  no neighbor 2001:DB8:ACAD:520::23 activate
  no neighbor 2001:DB8:ACAD:520::26 activate
  neighbor 95.188.10.2 activate
  neighbor 95.188.30.2 activate
  neighbor 172.24.52.23 activate
  neighbor 172.24.52.23 next-hop-self
  neighbor 172.24.52.26 activate
  neighbor 172.24.52.26 next-hop-self
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:5::1 activate
  neighbor 2001:DB8:ACAD:5::1 activate
  neighbor 2001:DB8:ACAD:520::23 activate
  neighbor 2001:DB8:ACAD:520::23 next-hop-self
  neighbor 2001:DB8:ACAD:520::26 activate
  neighbor 2001:DB8:ACAD:520::26 next-hop-self
 exit-address-family
!
```  

Настройки iBGP на R25  
```
router bgp 520
 bgp log-neighbor-changes
 neighbor 2001:DB8:ACAD:520::23 remote-as 520
 neighbor 2001:DB8:ACAD:520::23 update-source Loopback0
 neighbor 2001:DB8:ACAD:520::26 remote-as 520
 neighbor 2001:DB8:ACAD:520::26 update-source Loopback0
 neighbor 172.24.52.23 remote-as 520
 neighbor 172.24.52.23 update-source Loopback0
 neighbor 172.24.52.26 remote-as 520
 neighbor 172.24.52.26 update-source Loopback0
 !
 address-family ipv4
  network 10.0.0.0 mask 255.255.255.0
  redistribute rip
  no neighbor 2001:DB8:ACAD:520::23 activate
  no neighbor 2001:DB8:ACAD:520::26 activate
  neighbor 172.24.52.23 activate
  neighbor 172.24.52.23 next-hop-self
  neighbor 172.24.52.26 activate
  neighbor 172.24.52.26 next-hop-self
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB8:ACAD:520::23 activate
  neighbor 2001:DB8:ACAD:520::23 next-hop-self
  neighbor 2001:DB8:ACAD:520::26 activate
  neighbor 2001:DB8:ACAD:520::26 next-hop-self
 exit-address-family
!
```  

Настройки iBGP на R26  
```
router bgp 520
 bgp log-neighbor-changes
 neighbor 2001:DB8:ACAD:6::1 remote-as 2042
 neighbor 2001:DB8:ACAD:520::24 remote-as 520
 neighbor 2001:DB8:ACAD:520::24 update-source Loopback0
 neighbor 2001:DB8:ACAD:520::25 remote-as 520
 neighbor 2001:DB8:ACAD:520::25 update-source Loopback0
 neighbor 95.188.40.2 remote-as 2042
 neighbor 172.24.52.24 remote-as 520
 neighbor 172.24.52.24 update-source Loopback0
 neighbor 172.24.52.25 remote-as 520
 neighbor 172.24.52.25 update-source Loopback0
 !
 address-family ipv4
  no neighbor 2001:DB8:ACAD:6::1 activate
  no neighbor 2001:DB8:ACAD:520::24 activate
  no neighbor 2001:DB8:ACAD:520::25 activate
  neighbor 95.188.40.2 activate
  neighbor 172.24.52.24 activate
  neighbor 172.24.52.24 next-hop-self
  neighbor 172.24.52.25 activate
  neighbor 172.24.52.25 next-hop-self
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB8:ACAD:6::1 activate
  neighbor 2001:DB8:ACAD:520::24 activate
  neighbor 2001:DB8:ACAD:520::24 next-hop-self
  neighbor 2001:DB8:ACAD:520::25 activate
  neighbor 2001:DB8:ACAD:520::25 next-hop-self
 exit-address-family
!
```  

**Проверяем соседство на R23, R24, R25 & R26**  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/R23-iBGP.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/R24-iBGP.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/R25-iBGP.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/R26-iBGP.png)  

**3. Настройка офис Москва так, чтобы приоритетным провайдером стал Ламас.**  
Для это я использовал атрибут LOCAL-PREFERENCE и ROUTE-MAP на R14
```
route-map LOCAL-PREF permit 10
 set local-preference 160
```

```
address-family ipv4
  neighbor 172.18.10.15 route-map LOCAL-PREF in
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB8:ACAD:2::1001:15 route-map LOCAL-PREF in
 exit-address-family
!
```  

Проверяем таблицу BGP на R14 & R15  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/R14-BGP.png)  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/R15-BGP.png)  

Видим что все маршруты наружу идут через ISP Ламас (IPv4 - 95.188.1.1, IPv6 - 2001:DB7:ACAB:3::1)  

**4. Настройка офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.**  
Для этого сначала использовал PREFIX-LIST  
```
ip prefix-list NET-22-22 seq 5 permit 172.22.22.0/25
ip prefix-list NET-22-22 seq 10 permit 172.22.0.0/16
!
ip prefix-list NET-22-24 seq 5 permit 172.22.24.0/25
ip prefix-list NET-22-24 seq 10 permit 172.22.0.0/16
```  

Но трафик упорно идет через лучший маршрут. Поэтому в настройках BGP использовал -  bgp bestpath as-path multipath-relax и maximum-paths 2. Трафик в сети которые прилетают по BGP - балансируется:
**Трасировка до ПК в г. Москва (VPC1 172.18.12.10) с ПК в г. С.-Петербург (VPC8 172.22.22.6)**  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/%20TO-VPC1%20172.18.12.10.png)  
Видим что улетает через хоп 95.188.40.1

**Трасировка до ПК в г. Москва (VPC7 172.18.14.10) с ПК в г. С.-Петербург (VPC 172.22.24.6)**  
![](https://github.com/merkelev/neteng/blob/main/labs/10-iBGP/TO-VPC7%20172.18.14.10.png)  
Видим что улетает через хоп 95.188.30.1

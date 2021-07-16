### IPSec over DmVPN

**Схема сети**  
![](https://github.com/merkelev/neteng/blob/main/labs/14-IPSec-over-DmVPN/images/network.png)  

**Цели:**  
1. Настроить GRE поверх IPSec между офисами Москва и С.-Петербург  
2. Настроить DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги  

**Выполнение:**  
**Таблица адресов GRE туннелей**  
| Device   | Address        | Mask | Interface  |
| -------- | ---------      | ---- | ---------  |
| R15      | 109.226.218.80 | /32  | Loopback80 |
|          | 10.100.0.1     | /30  | Tunnel100  |
| R14      | 109.226.218.80 | /32  | Loopback80 |
|          | 10.100.0.1     | /30  | Tunnel100  |
| R18      | 75.100.20.80   | /32  | Loopback80 |
|          | 10.100.0.2     | /30  | Tunnel100  | 

**Таблица адресов DMVPN**  
| Device   | Address        | Mask | Interface  |
| -------- | ---------      | ---- | ---------  |
| R15      | 109.226.218.100| /30  | Loopback100|
|          | 10.200.0.1     | /24  | Tunnel100  |
| R14      | 95.188.0.2     | /30  | Et0/1      |
|          | 10.120.0.1     | /24  | Tunnel20   |
| R28      | 95.188.50.2    | /30  | Et0/0      |
|          | 10.200.0.3     | /24  | Tunnel10   |
|          | 95.188.100.2   | /30  | Et0/1      |
|          | 10.120.0.2     | /24  | Tunnel20   |
| R27      | 95.188.120.2   | /30  | Et0/0      |
|          | 10.200.0.2     | /24  | Tunnel10   |
|          | 10.120.0.3     | /24  | Tunnel20   |  

**1. Настройка GRE поверх IPSec между офисами Москва и С.-Петербург**  
**Настройка IPSec на R18**  
```
crypto isakmp policy 10
 encr aes
 hash sha256
 authentication pre-share
 group 2
crypto isakmp key SP address 109.226.218.80
!
crypto ipsec transform-set TS-IPSEC-MSK esp-aes
 mode transport
!
crypto ipsec profile IPSEC-MSK
 set transform-set TS-IPSEC-MSK
!
crypto map CM-GRE 10 ipsec-isakmp
 set peer 109.226.218.80
 match address ACL-GRE
!
```  

ACL на R18 - ACL-GRE  
```
ip access-list extended ACL-GRE
 permit gre host 75.100.20.80 host 109.226.218.80 log
 permit gre host 109.226.218.80 host 75.100.20.80 log
```  

Настройка туннеля  
```
interface Tunnel100
 description GRE-TUNNEL-TO-R15-MSK
 ip address 10.100.0.2 255.255.255.252
 no ip redirects
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 75.100.20.80
 tunnel destination 109.226.218.80
 tunnel protection ipsec profile IPSEC-MSK
```  

**Настройка IPSec на R14**  
```
crypto isakmp policy 10
 encr aes
 hash sha256
 authentication pre-share
 group 2
!
crypto isakmp key SP address 75.100.20.80
!
crypto ipsec transform-set TS-IPSEC-MSK esp-aes
 mode transport
!
crypto ipsec profile IPSEC-MSK
 set transform-set TS-IPSEC-MSK
!
crypto map CM-GRE 10 ipsec-isakmp
 set peer 109.226.218.80
 match address ACL-GRE
```  

ACL на R14 - ACL-GRE  
```
ip access-list extended ACL-GRE
 permit gre host 109.226.218.80 host 75.100.20.80 log
 permit gre host 75.100.20.80 host 109.226.218.80 log
```  

Настройка туннеля  
```
interface Tunnel100
 description GRE-TUNNEL-TO-R18-SP
 ip address 10.100.0.1 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 109.226.218.80
 tunnel destination 75.100.20.80
 tunnel protection ipsec profile IPSEC-MSK
```  

**Настройка IPSec на R15**  
```
crypto isakmp policy 10
 encr aes
 hash sha256
 authentication pre-share
 group 2
!
crypto isakmp key SP address 75.100.20.80
!
crypto ipsec transform-set TS-IPSEC-MSK esp-aes
 mode transport
!
crypto ipsec profile IPSEC-MSK
 set transform-set TS-IPSEC-MSK
!
crypto map CM-GRE 10 ipsec-isakmp
 set peer 109.226.218.80
 match address ACL-GRE
```  

ACL на R15 - ACL-GRE  
```
ip access-list extended ACL-GRE
 permit gre host 109.226.218.80 host 75.100.20.80 log
 permit gre host 75.100.20.80 host 109.226.218.80 log
```  

Настройка туннеля  
```
interface Tunnel100
 description GRE-TUNNEL-TO-R18-SP
 ip address 10.100.0.1 255.255.255.252
 no ip redirects
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 109.226.218.80
 tunnel destination 75.100.20.80
 tunnel protection ipsec profile IPSEC-MSK
```  

Проверяю состояние туннеля  
![](https://github.com/merkelev/neteng/blob/main/labs/14-IPSec-over-DmVPN/images/GRE-IPSEC.png)  

Туннель работает между R18 & R15  

**2. Настройка DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги**  
Настройка  CA на R19  
```
crypto pki server msk.ru
 no database archive
!
crypto pki trustpoint msk.ru
 enrollment url http://172.18.0.66:80
 subject-name O=MSK,C=RU
 revocation-check crl
 rsakeypair msk.ru
!
```  

Настройка получения сертификатов от CA-сервера на R14  
```
ip host msk.ru 172.18.0.66
!
crypto pki trustpoint msk.ru
 enrollment url http://msk.ru:80
 serial-number
 revocation-check crl
!
```  

Сертификаты на R14  
![](https://github.com/merkelev/neteng/blob/main/labs/14-IPSec-over-DmVPN/images/R14-CERT.png)  


Настройка получения сертификатов от CA-сервера на R15  
```
ip host msk.ru 172.18.0.66
!
crypto pki trustpoint msk.ru
 enrollment url http://msk.ru:80
 serial-number
 revocation-check crl
!
```  

Сертификаты на R15  
![](https://github.com/merkelev/neteng/blob/main/labs/14-IPSec-over-DmVPN/images/R15-CERT.png)  


Настройка получения сертификатов от CA-сервера на R27  
```
ip host msk.ru 109.226.218.180
!
crypto pki trustpoint msk.ru
 enrollment url http://msk.ru:80
 serial-number
 revocation-check crl
!
```  

Сертификаты на R27  
![](https://github.com/merkelev/neteng/blob/main/labs/14-IPSec-over-DmVPN/images/R27-CERT.png)  

Настройка получения сертификатов от CA-сервера на R28  
```
ip host msk.ru 109.226.218.180
!
crypto pki trustpoint msk.ru
 enrollment url http://msk.ru:80
 serial-number
 revocation-check crl
!
```  

Сертификаты на R28  
![](https://github.com/merkelev/neteng/blob/main/labs/14-IPSec-over-DmVPN/images/R28-CERT.png)  

### BGP. Фильтрация

**Цель: Настроить фильтрацию для офиса в Москве; Настроить фильтрацию для офиса в С.-Петербург.**  
**1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path);**  
**2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list);**    
**3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по-умолчанию;**  
**4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по-умолчанию и префикс офиса С.-Петербург;**    
**Все сети в лабораторной работе должны иметь IP связность;**  

![](https://github.com/merkelev/neteng/blob/main/labs/11-BGP-Filtration/NET.png)  

**1. Настройка фильтрации в офисе Москва так, чтобы не появилось транзитного трафика(As-path)**  
Настройки на R14  
acl as-path  
```
ip as-path access-list 1 permit ^$
ip as-path access-list 1 deny .*
```  
Настройки BGP  

```
router bgp 1001
 !
 address-family ipv4
  network 172.18.12.0 mask 255.255.255.128
  network 172.18.14.0 mask 255.255.255.128
  neighbor 95.188.0.1 filter-list 1 out
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:2::12:0/120
  network 2001:DB8:ACAD:2::14:0/120
  neighbor 2001:DB7:ACAB:1::2 filter-list 1 out
 exit-address-family
!
```  
Проверяем что мы отдаем соседу  
![](https://github.com/merkelev/neteng/blob/main/labs/11-BGP-Filtration/R14-ADVER.png)  

Настройки на R15  
acl as-path  
```
ip as-path access-list 1 permit ^$
ip as-path access-list 1 deny .*
```  
Настройки BGP  

```
router bgp 1001
 !
 address-family ipv4
  network 172.18.12.0 mask 255.255.255.128
  network 172.18.14.0 mask 255.255.255.128
  neighbor 95.188.1.1 filter-list 1 out
 exit-address-family
 !
 address-family ipv6
  network 2001:DB8:ACAD:2::12:0/120
  network 2001:DB8:ACAD:2::14:0/120
  neighbor 2001:DB7:ACAB:3::1 filter-list 1 out
 exit-address-family
!
```  
Проверяем что мы отдаем соседу  
![](https://github.com/merkelev/neteng/blob/main/labs/11-BGP-Filtration/R15-ADVER.png)  

**3. Настройка провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по-умолчанию.**  
Для этого на R22 создал prefix-list и route-map  
```
ip prefix-list DEFAULT seq 5 permit 0.0.0.0/0
!
ipv6 prefix-list DEFAULT-V6 seq 5 permit ::/0
route-map DEF-ROUTE permit 10
 match ip address prefix-list DEFAULT
!
route-map DEF-ROUTE-V6 permit 10
 match ipv6 address DEFAULT-V6
!
```  
И привязал route-map к соседу R14  
```
router bgp 101
 neighbor 2001:DB7:ACAB:1::1 remote-as 1001
 neighbor 95.188.0.2 remote-as 1001
 !
 address-family ipv4
  neighbor 95.188.0.2 default-originate
  neighbor 95.188.0.2 route-map DEF-ROUTE out
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:1::1 default-originate
  neighbor 2001:DB7:ACAB:1::1 route-map DEF-ROUTE-V6 out
 exit-address-family
!
```  

На R22 проверяем маршруты которые мы отдаем соседу R14  
![](https://github.com/merkelev/neteng/blob/main/labs/11-BGP-Filtration/R22-ADVER.png)  

Видим что ни каких префиксов не анонсируем - только дефолт.  

На R14 проверяем что мы получаем от соседа R22   
![](https://github.com/merkelev/neteng/blob/main/labs/11-BGP-Filtration/R14-ROUTES-BGP.png)  

Видим только дефолт.

**4. Настройка провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по-умолчанию и префикс офиса С.-Петербург**  
Для этого на R21 создал prefix-list и route-map  
```
ip prefix-list ROUTE-MSK seq 5 permit 172.22.0.0/16
ip prefix-list ROUTE-MSK seq 10 permit 0.0.0.0/0
!
!
ipv6 prefix-list ROUTE-MSK-V6 seq 5 permit 2001:DB8:ACAD:1::/64
ipv6 prefix-list ROUTE-MSK-V6 seq 10 permit ::/0
route-map RM-MSK-V6 permit 10
 match ipv6 address prefix-list ROUTE-MSK-V6
!
route-map RM-MSK permit 10
 match ip address prefix-list ROUTE-MSK
!
```  
И привязал route-map к соседу R15  
```
router bgp 301
 neighbor 2001:DB7:ACAB:3::2 remote-as 1001
 neighbor 95.188.1.2 remote-as 1001
 !
 address-family ipv4
  neighbor 95.188.1.2 default-originate
  neighbor 95.188.1.2 route-map RM-MSK out
 exit-address-family
 !
 address-family ipv6
  neighbor 2001:DB7:ACAB:3::2 default-originate
  neighbor 2001:DB7:ACAB:3::2 route-map RM-MSK-V6 out
 exit-address-family
!
```  

Проверяем маршруты которые мы отдаем соседу R15  
![](https://github.com/merkelev/neteng/blob/main/labs/11-BGP-Filtration/R21-ADVER.png)  

Видим что анонсируем префикс офиса С.-Петербург и дефолт.

На R15 проверяем что мы получаем от соседа R21  
![](https://github.com/merkelev/neteng/blob/main/labs/11-BGP-Filtration/R15-ROUTES-BGP.png)  

Видим префикс офиса С.-Петербург и дефолт.

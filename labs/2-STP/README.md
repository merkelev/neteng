### HOMEWORK STP

**Задание: Развертывание коммутируемой сети с резервными каналами.**  
**Цели:**  
**Часть 1. Создание сети и настройка основных параметров устройства**     
**Часть 2. Выбор корневого моста**    
**Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов**    
**Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов**    

![Топология сети в программе EVE-NG](https://github.com/merkelev/neteng/blob/main/labs/2-STP/STP-Topology.png)

**Таблица адресов IPv4**

| Device | Interface | IP Address | Subnet Mask |  
| -------- | ------------- | -------- | -------- |
| SW1 | VLAN1 | 192.168.1.1 | 255.255.255.0 |
| SW2 | VLAN1 | 192.168.1.2 | 255.255.255.0 |
| SW3 | VLAN1 | 192.168.1.3 | 255.255.255.0 |


**Выполнение**  
**Часть 1:**  
**Настроил IP адреса на коммутаторах на интерфейсе VLAN1**  

**Насройка IP адреса на коммутаторе SW1:**  
interface Vlan1  
 ip address 192.168.1.1 255.255.255.0  
!  

**Насройка IP адреса на коммутаторе SW2:**  
interface Vlan1  
 ip address 192.168.1.2 255.255.255.0  
!  

**Насройка IP адреса на коммутаторе SW3:**  
interface Vlan1  
 ip address 192.168.1.3 255.255.255.0  
!  

**Проверил доступность коммутаторв с помощью эхо-запросов:**  
**Ping с коммутатора SW1 до коммутаторов SW2 и SW3**  
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/ping-SW1-to-SW2%26SW3.png)  

**Ping с коммутатора SW2 до коммутатора SW3**   
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/ping-SW2-to-SW3.png)  

**Часть 2:**  
**Отключил все порты и сделал их транковыми на коммутаторах SW1, SW2 и SW3**  
**SW1:**  
interface GigabitEthernet0/0  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/1  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/2  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/3  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  

**SW2:**  
interface GigabitEthernet0/0  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/1  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/2  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/3  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  

**SW3:**  
interface GigabitEthernet0/0  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/1  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/2  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/3  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 shutdown  
 media-type rj45  
 negotiation auto  
!  

**На SW1 включил порты Gi0/0 в сторону SW2 и Gi0/2 в сторону SW3**  
interface GigabitEthernet0/0  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/2  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 media-type rj45  
 negotiation auto  
!  

**На SW2 включил порты Gi0/0 в сторону SW1 и Gi0/2 в сторону SW3**   
interface GigabitEthernet0/0  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/2  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 media-type rj45  
 negotiation auto  
!  

**На SW3 включил порты Gi0/0 в сторону SW2 и Gi0/2 в сторону SW1**  
interface GigabitEthernet0/0  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 media-type rj45  
 negotiation auto  
!  
interface GigabitEthernet0/2  
 switchport trunk encapsulation dot1q  
 switchport mode trunk  
 media-type rj45  
 negotiation auto  
!  

**ВЫВОДЫ:**  
**Данные протокола STP на коммутаторах**
**SW1**  
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/SW1-STP.png)  

**SW2**  
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/SW2-STP.png)  

**SW3**  
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/SW3-STP.png)  

**На основе поступающих данных, коммутатор SW1 является корневым мостом.**  
**Коммутатор SW1 был выбран корневым так как его MAC-адрес является наименьшим.**   
**Корневыми портами являются Gi0/0 на SW2 и Gi0/2 на SW3.**  
**Назначенными портами на SW1 являются Gi0/0, Gi0/2. Назначенным портом на SW2 является Gi0/2. На SW3 назначенных портов нет.**  
**Порт Gi0/0 на SW3 является альтернативными и заблокирован в данный момент, так как стоимость через порт Gi0/0 до корневого моста выше, чем через порт Gi0/2.**  

**Часть 3:**  
Коммутатор SW3 с заблокированным портом Gi0/2
**Изменил стоимость корневого порта Gi0/2 на SW3:**  
SW3(config)#int gigabitEthernet 0/2  
SW3(config-if)#spanning-tree cost 18  
На SW3 порт Gi0/2 стал заблокированным, а Gi0/0 - корневым. 
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/SW2-cost18.png)  
**Но это не то что требовалось показать основании материала методички.**  
**На SW2 уменьшаю стоимость корневого порта Gi0/0**   
**STP после изменения стоимость порта.**  
**SW2**  
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/SW2-after-cost18.png)  

**SW3**  
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/SW3-after-cost18.png)  

**STP после изменения стоимости порта на коммутаторе SW2, перевел порт Gi0/0 на коммутаторе SW3 в назначенный и заблокировал порт Gi0/0 на SW2 так как стоимость до корневого моста изменилась.**  

**Часть 4:**  
**После включения все портов на коммутаторах**  
**SW2**  
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/SW2-AllPorts.png)  

**SW3**  
![](https://github.com/merkelev/neteng/blob/main/labs/2-STP/SW3-AllPorts.png)  

**На коммутаторе SW2 в качестве корневого порта выбран Gi0/1, так как стоимость порта Gi0/0 составляет 18 а ID портов GI0/2, Gi0/3 больше ID порта Gi0/1.** 

**На коммутаторе SW3 в качестве корневого порта выбран Gi0/2, так как стоимость через порты Gi0/0, Gi0/1 выше чем через порт Gi0/2. Порт Gi0/3 не выбран в качестве корневого потому что ID порта Gi0/2 меньше ID порта Gi0/3.**  


Конфиг коммутатора SW1:  
(https://github.com/merkelev/neteng/blob/main/labs/2-STP/Config-SW1.txt)  

Конфиг коммутатора SW2:  
(https://github.com/merkelev/neteng/blob/main/labs/2-STP/Config-SW2.txt)  

Конфиг коммутатора SW3:  
(https://github.com/merkelev/neteng/blob/main/labs/2-STP/Config-SW3.txt)  

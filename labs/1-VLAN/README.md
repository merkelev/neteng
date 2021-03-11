### Homework VLAN

Топология в программе EVE-NG: Topology-VLAN-EVE-NG.png
![Топология в программе EVE-NG: Topology-VLAN-EVE-NG.png](https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/Topology-VLAN-EVE-NG.png) 

**1. Настройка маршрутизатора R1:**  
**Настроил порт GigabitEthernet 0/0 для VLAN 3,4 и 8**  

**Для VLAN 3:**    
- переход в настройку интерфейса: interface Gi0/0.3  
- установил описание: description MNGM  
- установил VID VLAN: encapsulation dot1Q 3  
- назнчил IP адрес и маску: ip address 192.168.3.1 255.255.255.0  
       
**Для VLAN 4:**  
- переход в настройку интерфейса: interface Gi0/0.4  
- установил описание: description OPERATIONS  
- установил VID VLAN: encapsulation dot1Q 4  
- назнчил IP адрес и маску: ip address 192.168.4.1 255.255.255.0  
       
**Для VLAN 8:**  
- переход в настройку интерфейса: interface Gi0/0.8  
  
**Приложение:**  
Конфиг маршрутизатора R1
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/Config-R1.txt)  
Роуты маршрутизатора R1
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/R1-Routes.png)

**2. Настройка коммутатора SW1:**  
**Настроил транковый порт GigabitEthernet 0/2 для VLAN 3,4 и 8 для соединения с R1:** 
- переход в настройку интерфейса: interface Gi 0/2  
- установил описание: description Trunk to R1  
- сделал порт транковым: switchport mode trunk  
- разрешил VLAN 3,4 и 8: switchport trunk allowed vlan 3,4,8  

**Настроил транковый порт GigabitEthernet 0/1 для VLAN 3,4,8 и сменил native VLAN:**  
- переход в настройку интерфейса: interface Gi 0/1  
- установил описание: description Trunk to SW2  
- сделал порт транковым: switchport mode trunk  
- разрешил VLAN 3,4 и 8: switchport trunk allowed vlan 3,4,8  
- сменил нативный вилан на 8: switchport trunk native vlan 8  

**Настроил акцес порт GigabitEthernet 0/0 для VLAN 3 для подключения PC4:**
- переход в настройку интерфейса: interface Gi 0/0  
- установил описание: description to PC-4  
- сделал порт акцесом: switchport mode access        
- разрешил VLAN 3: switchport access vlan 3  

**Приложение:**  
Конфиг коммутатора SW1
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/Config-SW1.txt)  
Настройки коммутатора SW1
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/SW1-Settings.png)  

**3. Настройка коммутатора SW2:**  
**Настроил транковый порт GigabitEthernet 0/1 для VLAN 3,4 и 8 для соединения с SW1:** 
- переход в настройку интерфейса: interface Gi 0/1  
- установил описание: description Trunk to SW1  
- сделал порт транковым: switchport mode trunk  
- разрешил VLAN 3,4 и 8: switchport trunk allowed vlan 3,4,8  
- сменил нативный вилан на 8: switchport trunk native vlan 8  

**Настроил акцес порт GigabitEthernet 0/0 для VLAN 4 для подключения PC5:**
- переход в настройку интерфейса: interface Gi 0/0  
- установил описание: description to PC-5  
- сделал порт акцесом: switchport mode access        
- разрешил VLAN 3: switchport access vlan 4  

**Приложение:**  
Конфиг коммутатора SW2
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/Config-SW2.txt)  
Настройки коммутатора SW2
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/SW2-Settings.png)


Таблица VLAN
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/VLAN%20Table.md)

Таблица адресов
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/Addressing%20Table.md)

Ping с PC4 до шлюза, ping с PC4 до PC5 и ping с PC4 до коммутатора SW2
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/PC4-PING-TO-GW-PC5.png)

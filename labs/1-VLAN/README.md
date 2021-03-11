### Homework VLAN

Топология в программе EVE-NG: Topology-VLAN-EVE-NG.png
![Топология в программе EVE-NG: Topology-VLAN-EVE-NG.png](https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/Topology-VLAN-EVE-NG.png)  

**Таблица адресов IPv4**

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
| -------- | ------------- | -------- | -------- | -------- |
| R1 | Gi0/0.3 | 192.168.3.1 | 255.255.255.0 | N/A |
|   | Gi0/0.4 | 192.168.4.1 | 255.255.255.0 | N/A  |
|   | Gi0/0.8 | N/A | N/A | N/A |
| SW1 | VLAN3 | 192.168.3.11 | 255.255.255.0 | 192.168.3.1 |
| SW2 | VLAN3 | 192.168.3.12 | 255.255.255.0 | 192.168.3.1 |
| PC4 | NIC | 192.168.3.3 | 255.255.255.0 | 192.168.3.1 |
| PC5 | NIC | 192.168.4.4 | 255.255.255.0 | 192.168.4.1 |


**Таблица VLAN**

| VLAN | Name | Interface Assigned |
| ------------- |:-------------:| ------------|
| 3 | MNGM | **SW1:** VLAN3, **SW1:** Gi0/0, **SW2:** VLAN3 | 
| 4 | OPERATIONS | **SW2:** Gi0/0 |
| 7 | PARKINGLOT | **SW1:** Gi0/3, Gi1/0-3 **SW2:** Gi0/2-3, Gi1/0-3 
| 8 | NATIVE | N/A |

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
- назначил порту VLAN 3: switchport access vlan 3  

**Настроил порты GigabitEthernet 0/3, GigabitEthernet 1/0 - 3 для VLAN 7 и отключил эти порты:**  
- переход в настройку интерфейса: interface Gi 0/3
- сделал порт акцесом: switchport mode access        
- назначил порту VLAN 7: switchport access vlan 7
- выключил порт: shutdown
- выход из настройки интерфейса: exit
- переход в настройку диапозона интерфейсов: interface range Gi 1/0 - 3
- сделал порты акцесом: switchport mode access        
- назначил портам VLAN 7: switchport access vlan 7
- выключил порты: shutdown

**Создал интерфейс VLAN 3, назначил ему IP адрес и указал дефолтный шлюз:**
- созлал интерфейс: interface vlan 3  
- назначил адрес: ip address 192.168.3.11 255.255.255.0  
- выход: exit  
- указал дефолтный шлюз: ip default-gateway 192.168.3.1  

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
- разрешил VLAN 4: switchport access vlan 4  

**Настроил порты GigabitEthernet 0/2 - 3, GigabitEthernet 1/0 - 3 для VLAN 7 и отключил эти порты:**  
- переход в настройку диапозона интерфейсов: interface Gi 0/2 - 3
- сделал порты акцесом: switchport mode access        
- назначил портам VLAN 7: switchport access vlan 7
- выключил порты: shutdown
- выход из настройки интерфейса: exit
- переход в настройку диапозона интерфейсов: interface range Gi 1/0 - 3
- сделал порты акцесом: switchport mode access        
- назначил портам VLAN 7: switchport access vlan 7
- выключил порты: shutdown

**Создал интерфейс VLAN 3, назначил ему IP адрес и указал дефолтный шлюз:**
- созлал интерфейс: interface vlan 3  
- назначил адрес: ip address 192.168.3.12 255.255.255.0  
- выход: exit  
- указал дефолтный шлюз: ip default-gateway 192.168.3.1  

**Приложение:**  
Конфиг коммутатора SW2
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/Config-SW2.txt)  
Настройки коммутатора SW2
(https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/SW2-Settings.png)

**4. На PC4 назначил IP адрес:**  
![](https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/PC4.png)  

**5. На PC5 назначил IP адрес:**  
![](https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/PC5.png)  

**6. Проверил маршрутизацию между VLAN, запустив ping с PC4 до PC5**  
![](https://github.com/merkelev/neteng/blob/main/labs/1-VLAN/pingPC4-PC5.png)

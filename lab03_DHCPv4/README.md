# **Введение в DHCPv4**

### **Задание**
1. Создание сети и конфигурирование базовых параметров устройств
2. Конфигурирование и проверка 2-х DHCP серверов на R1
3. Конфигурирование и проверка DHCP Relay на R2

### **Решение:**
Часть 1: Создание сети и конфигурирование базовых параметров устройств.
1. Установить схему адресации.
2. Подключение сети, как показано в топологии.
3. Настройка основных параметров для каждого маршрутизатора.
4. Настройка маршрутизации между VLAN на маршрутизаторе R1.
5. Настройка E0/1 на маршрутизаторе R2, затем E0/0 и статическуя маршрутизация обоих маршрутизаторов.
6. Настройка основных параметров для каждого коммутатора.
7. Создание VLAN-ов на S1.
8. Назначение VLAN-ов для соответствующих интерфейсов коммутатора.
9. Ручная настройка интерфейса S1 E0/1 как магистраль 802.1Q.

Часть 2: Конфигурирование и проверка 2-х DHCP серверов на R1
1. Настройка R1 с пулами DHCPv4 для двух поддерживаемых подсетей.
2. Сохранение конфигурации.
3. Проверка конфигурации сервера DHCPv4.
4. Попытка получить IP-адрес от DHCP на PC-A.

Часть 3:	Конфигурирование и проверка DHCP Relay на R2
1. Настройка R2 в качестве агента DHCP Relay для локальной сети на e0/1.
2. Попытка получить IP-адрес от DHCP на PC-B.

# **Часть 1: Создание сети и конфигурирование базовых параметров устройств**

### **1. Установить схему адресации**

Таблица адресации

|  Устройство  | Интерфейс | IP-адрес      | Маска подсети   |
|--------------|-----------|---------------|-----------------|
| R1           | E0/0      | 10.0.0.1      | 255.255.255.252 |
| R1           | E0/1.100  | 192.168.1.1   | 255.255.255.192 |
| R1           | E0/1.200  | 192.168.1.65  | 255.255.255.224 |
| R2           | E0/0      | 10.0.0.1      | 255.255.255.252 |
| R2           | E0/1      | 192.168.1.97  | 255.255.255.240 |
| S1           | VLAN200   | 192.168.1.66  | 255.255.255.224 |
| S2           | VLAN1     | 192.168.1.98  | 255.255.255.240 |

### **2. Подключение сети, как показано в топологии**

Схема в EVE-NG

![DHCPv4](https://github.com/bamboleilo/otus_nets/assets/39755453/ca418db8-2b46-4e4d-86af-8da813a4dd7c)

### **3. Настройка основных параметров для каждого маршрутизатора**

Пример настройки базовых параметров для R1

hostname R1
no ip domain-lookup
enable secret class
line console 0
 password cisco
 login
line vty 0 4
 password cisco
 login
 transport input all
service password-encryption 
banner motd #Authorized Access Only!#
clock timezone PST 3

### **4. Настройка маршрутизации между VLAN на маршрутизаторе R1**

interface Ethernet0/0
 ip address 10.0.0.1 255.255.255.252
 no shutdown
interface Ethernet0/1
 no shutdown
interface Ethernet0/1.100
 encapsulation dot1Q 100
 ip address 192.168.1.1 255.255.255.192
interface Ethernet0/1.200
 encapsulation dot1Q 200
 ip address 192.168.1.65 255.255.255.224
ip route 0.0.0.0 0.0.0.0 10.0.0.2

### **5. Настройка E0/1 на маршрутизаторе R2, затем E0/0 и статическуя маршрутизация обоих маршрутизаторов**

interface Ethernet0/1
 ip address 192.168.1.97 255.255.255.240
 no shutdown
interface Ethernet0/0
 ip address 10.0.0.2 255.255.255.252
 no shutdown
ip route 0.0.0.0 0.0.0.0 10.0.0.1
 

### **6. Настройка основных параметров для каждого коммутатора**

Пример настройки базовых параметров для S1

hostname S1
no ip domain-lookup
enable secret class
line console 0
 password cisco
 login
line vty 0 4
 password cisco
 login
 transport input all
service password-encryption 
banner motd #Authorized Access Only!#
clock timezone PST 3

### **7. Создание VLAN-ов на S1**

vlan 100
 name Client
vlan 200
 name Management
vlan 999
 name Parking_Lot
vlan 1000
 name Native


### **8. Назначение VLAN-ов для соответствующих интерфейсов коммутатора**

interface Ethernet0/0
 switchport access vlan 999
 switchport mode access
 shutdown
interface Ethernet0/2
 switchport access vlan 999
 switchport mode access
 shutdown
interface Ethernet0/3
 switchport access vlan 100
 switchport mode access


### **9. Ручная настройка интерфейса S1 E0/1 как магистраль 802.1Q**

interface Ethernet0/1
 switchport trunk allowed vlan 100,200,1000
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 1000
 switchport mode trunk


# **Часть 2: Конфигурирование и проверка 2-х DHCP серверов на R1**

### **1. Настройка R1 с пулами DHCPv4 для двух поддерживаемых подсетей**

ip dhcp excluded-address 192.168.1.1 192.168.1.5
ip dhcp excluded-address 192.168.1.97 192.168.1.101
ip dhcp pool LAN_A
 network 192.168.1.0 255.255.255.192
 domain-name ccna-lab.com
 default-router 192.168.1.1
 lease 2 12 30
ip dhcp pool R2_Client_LAN
 network 192.168.1.96 255.255.255.224
 domain-name ccna-lab.com
 default-router 192.168.1.97
 lease 2 12 30

### **2. Сохранение конфигурации**

copy running-config startup-config

### **3. Проверка конфигурации сервера DHCPv4**

![DHCP-verify](https://github.com/bamboleilo/otus_nets/assets/39755453/20a911ac-682d-41b3-bf22-a8c86c396870)


### **4. Попытка получить IP-адрес от DHCP на PC-A**

![PC-A](https://github.com/bamboleilo/otus_nets/assets/39755453/87822038-cb3f-45db-9140-89223083c242)

![PC-A_ping](https://github.com/bamboleilo/otus_nets/assets/39755453/5859b9e1-16c1-4753-b09f-cc5cc8c2058f)



# **Часть 3: Конфигурирование и проверка DHCP Relay на R2**

### **1. Настройка R2 в качестве агента DHCP Relay для локальной сети на e0/1**

interface Ethernet0/1
 ip helper-address 10.0.0.1
 
copy running-config startup-config

### **2. Попытка получить IP-адрес от DHCP на PC-B**

![PC-B](https://github.com/bamboleilo/otus_nets/assets/39755453/ce7ff8f2-b8c4-4cd6-b6fa-39651d6d94c8)

![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/72149952-3774-49cb-af5b-d3622d4ca487)

![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/c561ac5c-80cb-4ae3-93ec-8e0781c6ef44)



Все файлы конфигураций приведены [здесь](configs/)

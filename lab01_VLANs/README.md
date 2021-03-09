# **Настройка маршрутизации между VLAN с использованием конфигурации router-on-a-stick**

### **Задание**
1. Создать сеть и настроить основные параметры устройств
2. Создать сети VLAN и назначить порты коммутаторов
3. Настроить транк 802.1Q между коммутаторами.
4. Настроить маршрутизацию между сетями VLAN
5. Проверить работу маршрутизации между VLAN

### **Решение:**
1. Задокументируем: таблицу маршрутизации, таблицу VLAN и схему
2. Создание макета в симуляторе Packettracert и настройка базовых параметров устройств
3. Создание сетей VLAN и назначение портов. Настройка маршрутизации
4. Проверка работы маршрутизации

### **1. Задокументируем: таблицу маршрутизации, таблицу VLAN и схему**
Топология

![изображение](https://user-images.githubusercontent.com/39755453/110473263-296e2700-8100-11eb-882f-aeb17d85ff76.png)

Таблица адресации
|  Устройство | Интерфейс | IP-адрес      | Маска подсети   | Шлюз по умолчанию |
|-------------|-----------|---------------|-----------------|-------------------|
| R1          | G0/0/1.3  | 192.168.3.1   | 255.255.255.0   | —                 |
|             | G0/0/1.4  | 192.168.4.1   | 255.255.255.0   |                   |
|             | G0/0/1.8  | —             | —               |                   |
| S1          | VLAN 3    | 192.168.3.11  | 255.255.255.0   | 192.168.3.1       |
| S2          | VLAN 3    | 192.168.3.12  | 255.255.255.0   | 192.168.3.1       |
| PC-A        | NIC       | 192.168.3.3   | 255.255.255.0   | 192.168.3.1       |
| PC-B        | NIC       | 192.168.4.3   | 255.255.255.0   | 192.168.3.1       |

Таблица VLAN

|  VLAN       | Имя         | Назначенный интерфейс          |
|:------------|:------------|:-------------------------------|
|<br><br> 3               |<br><br>  Management    | S1: VLAN 3 <br> S2: VLAN 3 <br>  S1: F0/6|
| 4           | Operations  | S2: F0/18                      |
|<br> 7             |<br> ParkingLot              | S1: F0/2-4, F0/7-24, G0/1-2 <br> S2: F0/2-17, F0/19-24, G0/1-2    |
| 8           | Native      | —                              |

### **2. Создание макета в симуляторе Packettracert и настройка базовых параметров устройств**
Схема в Packettracert

![image](https://user-images.githubusercontent.com/39755453/110502572-22a1dd00-811d-11eb-92e6-479856f262dd.png)

###  Пример настройки на маршрутизаторе R1:

```
service password-encryption
!
hostname R1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
no ip domain-lookup
!
banner motd ^CAuthorized Access Only!^C
!
line con 0
 password 7 0822455D0A16
!
line aux 0
!
line vty 0 4
 password 7 0822455D0A16
 login
line vty 5 15
 password 7 0822455D0A16
 login
!
```

Аналогично с коммутаторами

### **3. Создание сетей VLAN и назначение портов. Настройка маршрутизации**

###  Пример настройки на коммутаторе S1:

```
!
interface FastEthernet0/1
 switchport trunk native vlan 8
 switchport trunk allowed vlan 3-4
 switchport mode trunk
!
interface FastEthernet0/2
 switchport access vlan 7
 switchport mode access
 shutdown
!
interface FastEthernet0/5
 switchport trunk allowed vlan 3-4,8
 switchport mode trunk
!
interface FastEthernet0/6
 switchport access vlan 3
 switchport mode access
!
interface Vlan3
 ip address 192.168.3.11 255.255.255.0
!
ip default-gateway 192.168.3.1
!

```

на втором коммутаторе аналогично, только свои вланы и нужные порты

###  Пример настройки на маршрутизаторе R1:

```
!
interface GigabitEthernet0/0/1.3
 description Management
 encapsulation dot1Q 3
 ip address 192.168.3.1 255.255.255.0
!
interface GigabitEthernet0/0/1.4
 description Operations
 encapsulation dot1Q 4
 ip address 192.168.4.1 255.255.255.0
!
interface GigabitEthernet0/0/1.8
 description Native
 encapsulation dot1Q 8
 no ip address
!
```

###  Пример настройки на PC-A:

![image](https://user-images.githubusercontent.com/39755453/110507424-d2794980-8121-11eb-83ca-95bf61c56998.png)

![image](https://user-images.githubusercontent.com/39755453/110507470-defda200-8121-11eb-8371-1d5c30ab0a9c.png)

###  Пример настройки на PC-B:

![image](https://user-images.githubusercontent.com/39755453/110507602-f9d01680-8121-11eb-8446-eae7fc0a6b0e.png)

![image](https://user-images.githubusercontent.com/39755453/110507654-048aab80-8122-11eb-99c2-4b10bc3d30bc.png)

Все файлы конфигураций приведены [здесь](configs/)

### **4. Проверка работы маршрутизации**

Проверка удаленного доступа:

![image](https://user-images.githubusercontent.com/39755453/110508218-998da480-8122-11eb-9784-2c0c63d9c91f.png)

Проверка связности с PC-B

![image](https://user-images.githubusercontent.com/39755453/110508377-c477f880-8122-11eb-9dca-7575ddfcdfb3.png)





# **Конфигурирование DHCPv6**

### **Задание**
1. Создание сети и настройка основных параметров устройств
2. Проверка назначения адреса SLAAC от маршрутизатора R1
3. Настройка и проверка сервера DHCPv6 без сохранения состояния на маршрутизаторе R1
4. Настройка и проверка сервера DHCPv6 с отслеживанием состояния на маршрутизаторе R1
5. Настройка и проверка ретрансляции DHCPv6 на маршрутизаторе R2

### **Решение:**
Часть 1: Создание сети и настройка основных параметров устройств
1. Подключение сети, как показано в топологии.
2. Настройка основных параметров для каждого коммутатора.
3. Настройка основных параметров для каждого маршрутизатора.
4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.

Часть 2: Проверка назначения адреса SLAAC от маршрутизатора R1

Часть 3:	Настройка и проверка сервера DHCPv6 без сохранения состояния на маршрутизаторе R1
1. Изучение конфигурации PC-A.
2. Настройка R1 для предоставления DHCPv6 без сохранения состояния для PC-A.

Часть 4: Настройка и проверка сервера DHCPv6 с отслеживанием состояния на маршрутизаторе R1

Часть 5: Настройка и проверка ретрансляции DHCPv6 на маршрутизаторе R2
1. Включиние компьютер PC-B и проверка адреса SLAAC, который он генерирует.
2. Настройте R2 в качестве агента ретрансляции DHCP для локальной сети на E0/1.
3. Попытка получить IPv6-адрес от DHCPv6 на PC-B.

# **Часть 1: Создание сети и настройка основных параметров устройств**

### **1. Подключение сети, как показано в топологии**

Схема в EVE-NG

![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/6e6c6f87-6074-4c80-b682-fa35cc659d75)


Таблица адресации

|  Устройство  | Интерфейс | IP-адрес                    | Тип адреса      |
|--------------|-----------|-----------------------------|-----------------|
| R1           | E0/0      | 2001:db8:acad:2::1 /64      | global          |
|              | E0/0      | fe80::1                     | link-local      |
|              | E0/1      | 2001:db8:acad:1::1 /64      | global          |
|              | E0/1      | fe80::1                     | link-local      |
| R2           | E0/0      | 2001:db8:acad:2::2 /64      | global          |
|              | E0/0      | fe80::2                     | link-local      |
|              | E0/1      | 2001:db8:acad:3::1 /64      | global          |
|              | E0/1      | fe80::1                     | link-local      |

### **2. Настройка основных параметров для каждого коммутатора**

Пример настройки базовых параметров для S1

```
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
interface range ethernet 0/2-3
 shutdown
copy running-config startup-config
```

### **3. Настройка основных параметров для каждого маршрутизатора**

Пример настройки базовых параметров для R1

```
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
ipv6 unicast-routing
copy running-config startup-config
```

### **4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов**

Пример настройки базовых параметров для R1

```
interface Ethernet0/0
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:2::1/64
interface Ethernet0/1
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
ipv6 route ::/0 2001:DB8:ACAD:2::2
copy running-config startup-config
```

# **Часть 2: Проверка назначения адреса SLAAC от маршрутизатора R1**
SLAAC - отработал, но скринов не сделал

# **Часть 3: Настройка и проверка сервера DHCPv6 без сохранения состояния на маршрутизаторе R1**

### **1. Изучение конфигурации PC-A**

![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/c043347d-176f-426a-8a6e-c4207b3ed28c)

### **2. Настройка R1 для предоставления DHCPv6 без сохранения состояния для PC-A**

```
ipv6 dhcp pool R1-STATELESS
 dns-server 2001:DB8:ACAD::254
 domain-name STATELESS.com
interface Ethernet0/1
 ipv6 nd other-config-flag
 ipv6 dhcp server R1-STATELESS
```
![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/db819c56-aac2-4345-8ced-546315fddf2c)

# **Часть 4: Настройка и проверка сервера DHCPv6 с отслеживанием состояния на маршрутизаторе R1**

```
ipv6 dhcp pool R2-STATEFULL
 address prefix 2001:DB8:ACAD:3:AAA::/80
 dns-server 2001:DB8:ACAD::254
 domain-name STATEFULL.com
interface Ethernet0/0
 ipv6 dhcp server R2-STATEFULL
```

# **Часть 5: Настройка и проверка ретрансляции DHCPv6 на маршрутизаторе R2**

### **1. Включиние компьютер PC-B и проверка адреса SLAAC, который он генерирует**

![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/3962fc8b-1f66-4273-809e-5e5a0d0dc5ca)

### **2. Настройте R2 в качестве агента ретрансляции DHCP для локальной сети на E0/1**

```
interface Ethernet0/1
 ipv6 nd managed-config-flag
 ipv6 dhcp relay destination 2001:DB8:ACAD:2::1 Ethernet0/0
```

### **3. Попытка получить IPv6-адрес от DHCPv6 на PC-B**

![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/e90739f6-c191-4bd3-9744-ee18fd34b800)

![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/55ba0417-64fe-4e68-bb20-ae1e261a7056)

![изображение](https://github.com/bamboleilo/otus_nets/assets/39755453/47071506-6b15-4113-9240-f8103d2c7217)


Все файлы конфигураций приведены [здесь](configs/)

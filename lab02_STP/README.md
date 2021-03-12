# **Развертывание коммутируемой сети с резервными каналами**

### **Задание**
1. Создание сети и настройка основных параметров устройства
2. Выбор корневого моста
3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета 

### **Решение:**
Часть 1: Создание сети и настройка основных параметров устройства
1. Создание макета в симуляторе Packettracert, таблицы адресации, настройка базовых параметров устройств, проверка работы
2. Настройка базовых параметро каждого коммутатора.
3. Проверка связи.

Часть 2: Определение корневого моста
1. Отключить все порты на коммутаторах
2. Настройть подключенные порты в качестве транковых.
3. Включить порты F0/2 и F0/4 на всех коммутаторах.
4. Отобразить данные протокола spanning-tree.

Часть 3:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
1. Определение коммутатора с заблокированным портом.
2. Измение стоимость порта.
3. Просмотр изменения протокола spanning-tree.
4. Удалить изменения стоимости порта.

Часть 4:	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

# **Часть 1: Создание сети и настройка основных параметров устройства**

### **1. Создание макета в симуляторе Packettracert, таблицы адресации, настройка базовых параметров устройств, проверка работы**

Схема в Packettracert

![изображение](https://user-images.githubusercontent.com/39755453/110939747-b4952a00-8357-11eb-9ef4-b626c2c6f589.png)

Таблица адресации

|  Устройство  | Интерфейс | IP-адрес      | Маска подсети   |
|--------------|-----------|---------------|-----------------|
| S1           | VLAN1     | 192.168.1.1   | 255.255.255.0   |
| S2           | VLAN1     | 192.168.1.2   | 255.255.255.0   |
| S3           | VLAN1     | 192.168.1.3   | 255.255.255.0   |


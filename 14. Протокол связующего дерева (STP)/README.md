# Оглавление
* [Схема стенда](#scheme)
* [Таблица адресации](#table1)
* [Часть 1. Создание сети и настройка основных параметров устройства](#part1)
* [Часть 2. Выбор корневого моста](#part2)
* [Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов](#part3)
* [Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов](#part4)
* [Вопросы для повторения](#questions)

# <a name="scheme"></a>Схема стенда
![](scheme.png)

*ВАЖНО: Схема, продиктованная термилабом отличается от той, что требуется в лабораторной работе.*

# <a name="table1"></a>Таблица адресации

| Устройство    | Интерфейс          | IP-адрес              | Маска подсети  |
| ------------- | :----------------: | :-------------------: | :------------- |
| S1            | VLAN 1             | 192.168.1.1           | 255.255.255.0  |
| S2            | VLAN 1             | 192.168.1.2           | 255.255.255.0  |
| S3            | VLAN 1             | 192.168.1.3           | 255.255.255.0  |

# <a name="part1"></a>Создание сети и настройка основных параметров устройства
## Создайте сеть согласно топологии
> Подключите устройства, как показано в топологии, и подсоедините необходимые кабели

Выполнено

## Выполните инициализацию и перезагрузку коммутаторов

Выполнено, также удален vlan.dat со всех устройств

## Настройте базовые параметры каждого коммутатора

> Отключите поиск DNS.
> Присвойте имена устройствам в соответствии с топологией.
> Назначьте class в качестве зашифрованного пароля доступа к привилегированному режиму.
> Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.
> Настройте logging synchronous для консольного канала.
> Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
> Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.
> Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

```shell
Switch>enable
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#enable secret class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line console 0
S1(config-line)#logging synchronous
S1(config-line)#exit
S1(config)#banner motd #Authorized Access Only!#
S1(config)#interface Vlan 1
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#end
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```shell
Switch>enable
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S2
S2(config)#enable secret class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line console 0
S2(config-line)#logging synchronous  
S2(config-line)#exit
S2(config)#banner motd #Authorized Access Only!#
S2(config)#interface Vlan 1
S2(config-if)#ip address 192.168.1.2 255.255.255.0
S2(config-if)#end
S2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```shell
Switch>enable
Switch#conf t
Switch(config)#no ip domain-lookup
Switch(config)#hostname S3
S3(config)#
S3(config)#enable secret class
S3(config)#line console 0
S3(config-line)#password cisco
S3(config-line)#login
S3(config-line)#logging synchronous
S3(config-line)#exit
S3(config)#line vty 0 15
S3(config-line)#password cisco
S3(config-line)#login
S3(config-line)#exit
S3(config)#banner motd #Authorized Access Only!#
S3(config)#interface Vlan 1
S3(config-if)#ip address 192.168.1.3 255.255.255.0
S3(config-if)#end
S3#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S3#
```

## Проверьте связь
> Проверьте способность компьютеров обмениваться эхо-запросами.
> Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?
> Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?
> Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?
> Выполняйте отладку до тех пор, пока ответы на все вопросы не будут положительными.

Для успешной связи необходимо также включить интерфейсы Vlan 1 на всех коммутаторах. 
После чего пинг пойдет.

```shell
S1#ping 192.168.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/3/9 ms

S1#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms

S2#ping 192.168.1.3
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms
```

# <a name="part2"></a>Выбор корневого моста
> Отключите все порты на коммутаторах

```shell
S1(config)#interface range f0/1-24
S1(config-if-range)#shutdown
S1(config-if-range)#interface range g0/1-2
S1(config-if-range)#shutdown
```

```shell
S2(config)#interface range f0/1-24
S2(config-if-range)#shutdown
S2(config-if-range)#interface range g0/1-2
S2(config-if-range)#shutdown
```

```shell
S3(config)#interface range f0/1-24
S3(config-if-range)#shutdown
S3(config-if-range)#interface range g0/1-2
S3(config-if-range)#shutdown
```

> Настройте подключенные порты в качестве транковых.

```shell
S1(config)#interface range f0/1-4
S1(config-if-range)#switchport mode trunk
```

```shell
S2(config-if-range)#interface range f0/1-4
S2(config-if-range)#switchport mode trunk
```

```shell
S3(config-if-range)#interface range f0/1-4 
S3(config-if-range)#switchport mode trunk
```

> Включите порты F0/2 и F0/4 на всех коммутаторах.

```shell
S1(config)#interface range f0/2,f0/4
S1(config-if-range)#no shutdown
```

```shell
S2(config)#interface range f0/2,f0/4
S2(config-if-range)#no shutdown
```

```shell
S3(config)#interface range f0/2,f0/4
S3(config-if-range)#no shutdown
```

> Отобразите данные протокола spanning-tree.

```shell
S1#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     ecc8.82dd.4980
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Altn BLK 19        128.2    P2p 
Fa0/4               Root FWD 19        128.4    P2p
```

```shell
S2#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     1c1d.8686.1d80
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Desg FWD 19        128.2    P2p 
Fa0/4               Desg FWD 19        128.4    P2p 
```

```shell
S3#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             Cost        19
             Port        2 (FastEthernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     c025.5c31.8c80
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Root FWD 19        128.2    P2p 
Fa0/4               Desg FWD 19        128.4    P2p
```

> В схему ниже запишите роль и состояние (Sts) активных портов на каждом коммутаторе в топологии

| Устройство    | Порт          | Роль          | Состояние   |
| ------------- | :-----------: | :------------ | :---------- |
| S1            | Fa0/2         | Alternative   | Blocking    |
|               | Fa0/4         | Root          | Forwarding  |
| S2            | Fa0/2         | Designated    | Forwarding  |
|               | Fa0/4         | Designated    | Forwarding  |
| S3            | Fa0/2         | Root          | Forwarding  |
|               | Fa0/4         | Designated    | Forwarding  |

> С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.

> Какой коммутатор является корневым мостом?

S2.

> Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?

BID Priority у всех коммутаторов одинаковый (32769), поэтому выбирается коммутатор с наименьшим MAC.

> Какие порты на коммутаторе являются корневыми портами?

> Какие порты на коммутаторе являются назначенными портами?

> Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?

См. таблицу выше.

> Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?

* S3: Стоимость до корневого моста (S2) на коммутаторе S3 с порта Fa0/4 выше, чем с порта Fa0/2. 
  Следовательно Fa0/4 выбран как назначенный (Designated), а Fa0/2 корневой (Root).
* S1: порт Fa0/2 устройства S1 подключен к порту Fa0/4 (Designated) коммутатора S3. 
  Следовательно, порт Fa0/2 будет заблокирован.

# <a name="part3"></a>Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
## Определите коммутатор с заблокированным портом

S1, порт Fa0/2

## Измените стоимость порта

В задании подразумевается изменение стоимости незаблокированного порта.

```shell
S1(config)#interface Fa0/4
S1(config-if)#spanning-tree cost 18
```

## Просмотрите изменения протокола spanning-tree

```shell
S1#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             Cost        18
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     ecc8.82dd.4980
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Desg FWD 19        128.2    P2p 
Fa0/4               Root FWD 18        128.4    P2p 
```

```shell
S3#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             Cost        19
             Port        2 (FastEthernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     c025.5c31.8c80
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Root FWD 19        128.2    P2p 
Fa0/4               Altn BLK 19        128.4    P2p 
```

> Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, 
> который был назначенным портом на другом коммутаторе?

Так как изменилась стоимость порта Fa0/4 на коммутаторе S1, 
следовательно с любого порта стоимость до корневого моста будет меньше, 
поэтому порт Fa0/2 станет назначенным, а порт Fa0/4 на коммутаторе S2 будет заблокирован.

## Удалите изменения стоимости порта
> Выполните команду no spanning-tree cost 18 режима конфигурации интерфейса, 
> чтобы удалить запись стоимости, созданную ранее.

```shell
S1(config)#interface Fa0/4
S1(config-if)#no spanning-tree cost 18
```

> Повторно выполните команду show spanning-tree, чтобы подтвердить, 
> что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. 
> Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта.

```shell
S1#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             Cost        19
             Port        4 (FastEthernet0/4)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     ecc8.82dd.4980
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Altn BLK 19        128.2    P2p 
Fa0/4               Root FWD 19        128.4    P2p
```

```shell
S2#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     1c1d.8686.1d80
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/2               Desg FWD 19        128.2    P2p 
Fa0/4               Desg FWD 19        128.4    P2p
```

# <a name="part4"></a>Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
> Включите порты F0/1 и F0/3 на всех коммутаторах

```shell
S1(config)#interface range f0/1,f0/3
S1(config-if-range)#no shutdown
```

```shell
S2(config)#interface range f0/1,f0/3
S2(config-if-range)#no shutdown
```

```shell
S3(config)#interface range f0/1,f0/3
S3(config-if-range)#no shutdown
```

> Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, 
> после чего выполните команду show spanning-tree на коммутаторах некорневого моста. 
> Обратите внимание, что порт корневого моста переместился на порт с меньшим номером, 
> связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста

```shell
S1#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             Cost        19
             Port        3 (FastEthernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     ecc8.82dd.4980
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/1               Altn BLK 19        128.1    P2p 
Fa0/2               Altn BLK 19        128.2    P2p 
Fa0/3               Root FWD 19        128.3    P2p 
Fa0/4               Altn BLK 19        128.4    P2p
```

```shell
S3#show spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     1c1d.8686.1d80
             Cost        19
             Port        1 (FastEthernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     c025.5c31.8c80
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec
Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/1               Root FWD 19        128.1    P2p 
Fa0/2               Altn BLK 19        128.2    P2p 
Fa0/3               Desg FWD 19        128.3    P2p 
Fa0/4               Desg FWD 19        128.4    P2p
```

> Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста?

Были выбраны порты с наименьшими номерами отправителя (Sender's Port ID), а другие заблокированы.
* S1: Fa0/4 -> Fa0/3 (Sender's Port Id - S2: F0/3)
* S3: Fa0/2 -> Fa0/1 (Sender's Port Id - S2: F0/1)

> Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?

Так как приоритеты портов одинаковые, то учитывался номер порта.

# <a name="questions"></a>Вопросы для повторения
> Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?

Значение Internal Root Path Cost (стоимость до корневого моста).
Порт с минимальной стоимостью до корневого моста будет назначен как Root (корневой порт).

> Если первое значение на двух портах одинаково, 
> какое следующее значение будет использовать протокол STP при выборе порта?

Sender's BID. То есть идентификатор того моста, чей BID ниже - тот порт будет работать в качестве Root port.
Причем это именно порты другого устройства (Sender's Port ID).

> Если оба значения на двух портах равны, каким будет следующее значение, 
> которое использует протокол STP при выборе порта?

Если значение стоимости до корневого моста у портов одинаковое,
то будет учитываться Sender's Port Priority (приоритет порта).
В последнем случае, Sender's Port ID (номер порта).
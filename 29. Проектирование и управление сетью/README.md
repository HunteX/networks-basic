# Оглавление
* [Схема стенда](#scheme)
* [Таблица адресации](#table1)
* [Часть 1. Создание сети и настройка основных параметров устройства](#part1)
* [Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP](#part2)
* [Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP](#part3)
* [Часть 4. Настройка и проверка NTP](#part4)

# <a name="scheme"></a>Схема стенда
![](scheme.png)

# <a name="table1"></a>Таблица адресации
| Устройство    | Интерфейс          | IP-адрес        | Маска подсети   | Шлюз по умолчанию |
| ------------- | :----------------: | :-------------: | :-------------- | :---------------- |
| R1            | Loopback1          | 172.16.1.1      | 255.255.255.0   | —                 |
|               | G0/0/1             | 10.22.0.1       | 255.255.255.0   |                   |
| S1            | SVI VLAN 1         | 10.22.0.2       | 255.255.255.0   | 10.22.0.1         |
| S2            | SVI VLAN 1         | 10.22.0.3       | 255.255.255.0   | 10.22.0.1         |

# <a name="part1"></a>Часть 1. Создание сети и настройка основных параметров устройства
## Шаг 1.1. Создайте сеть согласно топологии.

Выполнено.

## Шаг 1.2. Произведите базовую настройку маршрутизаторов.

Выполнено.

## Шаг 1.3. Настройте базовые параметры каждого коммутатора.

Выполнено.

# <a name="part2"></a>Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP
> 2.1.a. На R1 используйте соответствующую команду show cdp, чтобы определить, 
 сколько интерфейсов включено CDP, сколько из них включено и сколько отключено.

```shell
R1#show cdp           
Global CDP information:
Sending CDP packets every 60 seconds
Sending a holdtime value of 180 seconds
Sending CDPv2 advertisements is  enabled
R1#show cdp neighbors 
Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
                  S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone, 
                  D - Remote, C - CVTA, M - Two-port Mac Relay 
Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
S1               Gig 0/0/1         177              S I   WS-C2960+ Fas 0/5
Total cdp entries displayed : 1
```

> Вопрос: Сколько интерфейсов участвует в объявлениях CDP? Какие из них активны?

Предположу, что нужно выполнять команду `show cdp neighbors`.
Единственный интерфейс g0/0/1 участвует в объявлениях и он активен.

> 1.1.a. На R1 используйте соответствующую команду show cdp, чтобы определить версию IOS, используемую на S1.

```shell
R1#show cdp entry S1
-------------------------
Device ID: S1
Entry address(es): 
Platform: cisco WS-C2960+24TC-L,  Capabilities: Switch IGMP 
Interface: GigabitEthernet0/0/1,  Port ID (outgoing port): FastEthernet0/5
Holdtime : 143 sec
Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.2(2)E9, RELEASE SOFTWARE (fc4)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2018 by Cisco Systems, Inc.
Compiled Sat 08-Sep-18 16:34 by prod_rel_team
advertisement version: 2
Protocol Hello:  OUI=0x00000C, Protocol ID=0x0112; payload len=27, value=00000000FFFFFFFF010221FF000000000000C0255C318C80FF0000
VTP Management Domain: 'SWITCH'
Native VLAN: 1
Duplex: full
```

> Какая версия IOS используется на  S1?

Version 15.2(2)E9

> 1.1.a. На S1 используйте соответствующую команду show cdp, чтобы определить, сколько пакетов CDP было выданных

```shell
S1#show cdp traffic 
CDP counters :
Total packets output: 102, Input: 30
Hdr syntax: 0, Chksum error: 0, Encaps failed: 0
No memory: 0, Invalid packet: 0, 
CDP version 1 advertisements output: 0, Input: 0
CDP version 2 advertisements output: 102, Input: 30
```

> Сколько пакетов имеет выход CDP с момента последнего сброса счетчика?

102

> 1.1.a. Настройте SVI для VLAN 1 на S1 и S2, используя IP-адреса, указанные в таблице адресации выше.
  Настройте шлюз по умолчанию для каждого коммутатора на основе таблицы адресов

```shell
S1(config)#interface vlan1
S1(config-if)#ip address 10.22.0.2 255.255.255.0
S1(config-if)#no shutdown
S1(config-if)#exit
S1(config)#ip default-gateway 10.22.0.1
```

```shell
S2(config)#interface vlan1
S2(config-if)#ip address 10.22.0.3 255.255.255.0
S2(config-if)#no shutdown
S2(config-if)#exit
S2(config)#ip default-gateway 10.22.0.1
```

> 1.1.b. На R1 выполните команду show cdp entry S1 .

```shell
R1#show cdp entry S1
-------------------------
Device ID: S1
Entry address(es): 
  IP address: 10.22.0.2
Platform: cisco WS-C2960+24TC-L,  Capabilities: Switch IGMP 
Interface: GigabitEthernet0/0/1,  Port ID (outgoing port): FastEthernet0/5
Holdtime : 138 sec
Version :
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.2(2)E9, RELEASE SOFTWARE (fc4)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2018 by Cisco Systems, Inc.
Compiled Sat 08-Sep-18 16:34 by prod_rel_team
advertisement version: 2
Protocol Hello:  OUI=0x00000C, Protocol ID=0x0112; payload len=27, value=00000000FFFFFFFF010221FF000000000000C0255C318C80FF0000
VTP Management Domain: 'SWITCH'
Native VLAN: 1
Duplex: full
Management address(es): 
  IP address: 10.22.0.2
```

> Вопрос: Какие дополнительные сведения доступны теперь?

В Entry address(es) и Management address(es) добавился адрес 10.22.0.2

> 1.1.a. Отключить CDP глобально на всех устройствах.

Выполнено.

# <a name="part3"></a>Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP

> 2.1.a. Введите соответствующую команду lldp run, чтобы включить LLDP на всех устройствах в топологии

Выполнено.

> 2.1.b. На S1 выполните соответствующую команду lldp, чтобы предоставить подробную информацию о S2.

```shell
S1#show lldp entry S2
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
------------------------------------------------
Local Intf: Fa0/1
Chassis id: 1c1d.8686.1d80
Port id: Fa0/1
Port Description: FastEthernet0/1
System Name: S2
System Description: 
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.2(2)E9, RELEASE SOFTWARE (fc4)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2018 by Cisco Systems, Inc.
Compiled Sat 08-Sep-18 16:34 by prod_rel_team
Time remaining: 113 seconds
System Capabilities: B
Enabled Capabilities: B
Management Addresses:
    IP: 10.22.0.3
Auto Negotiation - supported, enabled
Physical media capabilities:
    100base-TX(FD)
    100base-TX(HD)
    10base-T(FD)
    10base-T(HD)
Media Attachment Unit type: 16
Vlan ID: 1
          
          
Total entries displayed: 1
```

> Что такое chassis ID  для коммутатора S2?

Судя по всему - это MAC-адрес порта fa0/1 на S2, но уменьшенный на 1

```shell
S2#show interfaces fa0/1            
FastEthernet0/1 is up, line protocol is up (connected) 
  Hardware is Fast Ethernet, address is 1c1d.8686.1d81 (bia 1c1d.8686.1d81)
```

> 1.1.a. Соединитесь через консоль на всех устройствах и используйте команды LLDP, 
 необходимые для отображения топологии физической сети только из выходных данных команды show

```shell
R1#show lldp neighbors 
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
S1                  Gi0/0/1        120        B               Fa0/5
Total entries displayed: 1
```

```shell
S1#show lldp neighbors 
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
S2                  Fa0/1          120        B               Fa0/1
R1                  Fa0/5          120        R               Gi0/0/1
Total entries displayed: 2
```

```shell
S2#show lldp neighbors        
Capability codes:
    (R) Router, (B) Bridge, (T) Telephone, (C) DOCSIS Cable Device
    (W) WLAN Access Point, (P) Repeater, (S) Station, (O) Other
Device ID           Local Intf     Hold-time  Capability      Port ID
S1                  Fa0/1          120        B               Fa0/1
Total entries displayed: 1
```

# <a name="part4"></a>Часть 4. Настройка и проверка NTP
## Шаг 2.1. Выведите на экран текущее время.

> Введите команду show clock для отображения текущего времени на R1.
  Запишите отображаемые сведения о текущем времени в следующей таблице

| Дата          | Время              | Часовой пояс    | Источник времени   |
| ------------- | :----------------: | :-------------: | :----------------- |
| Nov 17        | 17:59:39.900 2021  | UTC             | —                  |

## Шаг 2.2. Установите время

> С помощью команды clock set установите время на маршрутизаторе R1. Введенное время должно быть в формате UTC

```shell
R1#clock set 20:58:00 17 Nov 2021 
*Nov 17 20:58:00.000: %SYS-6-CLOCKUPDATE: System clock has been updated from 18:03:59 UTC Wed Nov 17 2021 to 20:58:00 UTC Wed Nov 17 2021, configured from console by consol
```

## Шаг 2.3. Настройте главный сервер NTP

> Настройте R1 в качестве хозяина NTP с уровнем слоя 4

```shell
R1(config)#ntp server 10.22.0.1
R1(config)#int g0/0/1
R1(config-if)#ntp broadcast
R1(config)#end
R1#show clock detail
21:11:51.844 UTC Wed Nov 17 2021
Time source is NTP
```

## Шаг 2.4. Настройте клиент NTP

> 2.4.a. Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время. Запишите текущее время,  в следующей таблице

| Устройство | Дата          | Время              | Часовой пояс    |
| :--------- | ------------- | :----------------: | :-------------: |
| S1         | Mar 1 1993    | 00:59:02.240       | UTC             |
| S2         | Mar 1 1993    | 01:06:39.755       | UTC             |

> 2.4.b. Настройте S1 и S2 в качестве клиентов NTP. 
  Используйте соответствующие команды NTP для получения времени от интерфейса G0/0/1 R1, 
 а также для периодического обновления календаря или аппаратных часов коммутатора

```shell
S1(config)#ntp server 10.22.0.1
```

```shell
S2(config)#ntp server 10.22.0.1
```

## Шаг 2.5. Проверьте настройку NTP

> 2.5.a. Используйте соответствующую команду show , чтобы убедиться, что S1 и S2 синхронизированы с R1

```shell
S1#show ntp status
Clock is synchronized, stratum 5, reference is 10.22.0.1      
nominal freq is 119.2092 Hz, actual freq is 119.2688 Hz, precision is 2**19
ntp uptime is 175300 (1/100 of seconds), resolution is 8403
reference time is E53FB079.B903DB0D (16:46:49.722 UTC Wed Nov 17 2021)
clock offset is -0.6093 msec, root delay is 2.21 msec
root dispersion is 7940.93 msec, peer dispersion is 3938.24 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is -0.000499999 s/s
system poll interval is 64, last update was 11 sec ago.

S1#show ntp associations 
  address         ref clock       st   when   poll reach  delay  offset   disp
*~10.22.0.1       127.127.1.1      4      1     64     1  2.211  -0.609 1938.4
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

```shell
S2#show ntp status
Clock is synchronized, stratum 5, reference is 10.22.0.1      
nominal freq is 119.2092 Hz, actual freq is 119.2139 Hz, precision is 2**19
ntp uptime is 144400 (1/100 of seconds), resolution is 8403
reference time is E53FAFCE.D406353B (16:43:58.828 UTC Wed Nov 17 2021)
clock offset is -0.3431 msec, root delay is 1.75 msec
root dispersion is 7941.26 msec, peer dispersion is 1938.73 msec
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is -0.000038741 s/s
system poll interval is 64, last update was 46 sec ago.

S2#show ntp associations 
  address         ref clock       st   when   poll reach  delay  offset   disp
*~10.22.0.1       127.127.1.1      4     13     64     1  1.752  -0.343 1938.7
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
```

> 2.5.b. Выполните соответствующую команду на S1 и S2, чтобы просмотреть настроенное время и сравнить ранее записанное время

```shell
S1#show clock detail 
16:48:10.368 UTC Wed Nov 17 2021
Time source is NTP
```

```shell
S2#show clock detail 
16:48:10.844 UTC Wed Nov 17 2021
Time source is NTP
```

# Вопрос для повторения

> Для каких интерфейсов в пределах сети не следует использовать протоколы обнаружения сетевых ресурсов? Поясните ответ

Точно не стоит использовать для тех, которые подключены к конечным устройствам.
Но в идеале, если нет острой необходимости, то стоит отключить везде.

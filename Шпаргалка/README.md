# Оглавление
* [Пароли](#passwords)
* [Конфигурация SSH](#ssh)
* [Предварительная конфигурация](#preconfig)
* [VLANs](#vlans)
* [STP](#stp)
* [EtherChannel](#etherchannel)
* [HSRP](#hsrp)

# <a name="passwords"></a>Пароли
## Пароль на privileged mode

```shell
Switch#configure terminal
Switch(config)#enable secret cisco
```

## Пароль на console line

```shell
Switch(config)#line console 0
Switch(config-line)#password class
Switch(config-line)#login
```

## Пароль на vty line

```shell
Switch(config)#line vty 1 15
Switch(config-line)#password class
Switch(config-line)#login
```

# <a name="ssh">Конфигурация SSH
```shell
R1(config)#ip domain name domain.local
R1(config)#crypto key generate rsa general-keys modulus 2048
R1(config)#username admin privilege 15 secret Adm1nP@55
R1(config)#ip ssh version 2
R1(config)#line vty 0 15
R1(config-line)#transport input all
R1(config-line)#login local
```

# <a name="security">Дополнительная защита
```shell
Router(config)#service password-encryption 
Router(config)#security passwords min-length 8
Router(config)#login block-for 120 attempts 3 within 60
Router(config)#line vty 0 15
Router(config-line)#password cisco12345678
Router(config-line)#exec-timeout 5 30
Router(config-line)#transport input ssh
Router(config-line)#end
```

# <a name="preconfig">Предварительная конфигурация
* Выключение поиска DNS
* Настройка logging synchronous для консольного канала
* Настройка баннерного сообщение дня (MOTD)
* Установка hostname
* Установка времени
* Пароли и их шифрование
* SVI, настройка шлюза по умолчанию
* Сохранение файла конфигурации

# Команды show
| Команда                        | Описание                                          |
| ------------------------------ | :------------------------------------------------ |
| show ip interface brief        | Все интерфейсы, их IP-адреса и состояние          |
| show ipv6 interface brief      | Все интерфейсы, их IPv6-адреса и состояние        |
| show ip route                  | Таблица маршрутизации                             |
| show ipv6 route                | Таблица маршрутизации IPv6                        |
| show interfaces                | Статистика по интерфейсам (только IPv4)           |
| show ip interfaces             | Статистика по IPv4 интерфейсам                    |
| show ipv6 interfaces           | Статистика по IPv6 интерфейсам                    |
| show ip arp                    | Отображает ARP-таблицу                            |
| show vlan                      | Полная информация по VLANs                        |
| show vlan brief                | Краткая сводка                                    |

# <a name="vlans">VLANs
## Команды
| Команда                                             | Описание                                                 |
| --------------------------------------------------- | :------------------------------------------------------- |
| vlan <vlan-id>                                      | Создает vlan                                             |
| name <vlan-name>                                    | Задает имя vlan                                          |
| show vlan                                           | Полная информация по VLANs                               |
| show vlan brief                                     | Краткая сводка                                           |
| show vlan summary                                   | Общая информация по vlan                                 |
| show interface vlan <vlan-id>                       | Отображает информацию по конкретному vlan                |
| show interfaces <interface-id> switchport           | Отображает информацию vlan на выбранном интерфейсе       |
| show interfaces trunk                               | Отображает информацию интерфейсов с режимом trunk        |
| switchport mode access                              | Задает access-режим работы интерфейса                    |
| switchport mode trunk                               | Задает trunk-режим работы интерфейса                     |
| switchport access vlan <vlan-id>                    | Задает vlan на конкретный интерфейс                      |
| switchport voice vlan <vlan-id>                     | Задает voice vlan                                        |
| switchport trunk native vlan <vlan-id>              | Задает native vlan (в режиме trunk)                      |
| switchport trunk allowed vlan <vlan-id>,<vlan-id2>  | Указывает список vlan разрешенных в режиме trunk         |
| switchport trunk allowed vlan add <vlan-id>         | Добавляет новый vlan в существующий список               |
| delete flash:vlan.dat                               | Удаляет файл с конфигурацией VLANs                       |

## Router-On-Stick
```shell
interface g0/0/1.1
encapsulation dot1Q 1
interface g0/0/1
no shutdown
```

# <a name="stp">STP
## Команды
| Команда                                             | Описание                                                 |
| --------------------------------------------------- | :------------------------------------------------------- |
| show spanning-tree                                  | Общая информация по STP                                  |
| show spanning-tree vlan <vlan-id>                   | Информация по STP на конкретном VLAN                     |
| show spanning-tree summary                          | Статистика по STP и интерфейсам                          |
| spanning-tree mode <proto>                          | Задает протокол, "pvst" или "rapid-pvst"                 |
| spanning-tree cost <1-200000000>                    | Задает стоимость пути на интерфейсе                      |
| spanning-tree VLAN <vlan-id> root primary           | Задает vlan определенный приоритет в BID (root bridge)   |
| spanning-tree VLAN <vlan-id> root secondary         | Задает vlan определенный приоритет в BID (secondary)     |
| spanning-tree VLAN <vlan-id> priority <value>       | Задает vlan приоритет в BID (custom value)               |
| spanning-tree portfast                              | Включает PortFast на интерфейсе                          |
| spanning-tree bpduguard enable                      | Включает bpduguard                                       |
| spanning-tree link-type point-to-point              | Full-duplex                                              |
| spanning-tree link-type shared                      | Half-duplex                                              |

# <a name="etherchannel">EtherChannel
## Команды
| Команда                                             | Описание                                                       |
| --------------------------------------------------- | :------------------------------------------------------------- |
| channel-group <id> mode desirable                   | Создает агрегированный канал для выбранных интерфейсов (PAgP)  |
| channel-group <id> mode active                      | Создает агрегированный канал для выбранных интерфейсов (LACP)  |
| interface port-channel <id>                         | Настройка агрегированный канал                                 |
| show interfaces port-channel <id>                   | Отображает статус по агрегированному каналу                    |
| show etherchannel summary                           | Сводка по EtherChannel                                         |
| show etherchannel port-channel                      | Отображает информацию по агрегированному каналу                |
| show interfaces <fid> etherchannel                  | Отображает роль выбранного интерфейса в EtherChannel           |

# <a name="hsrp">HSRP
## Команды
| Команда                                             | Описание                                                       |
| --------------------------------------------------- | :------------------------------------------------------------- |
| standby version 2                                   | Задает версию HSRP                                             |
| standby <group-number> ip <ip>                      | Задает виртуальный IP для выбранной группы                     |
| standby <group-number> priority <value>             | Задает приоритет маршрутизатору                                |
| standby <group-number> preempt                      | Задает приоритетное вытеснение                                 |
| show standby                                        | Информация по HSRP                                             |
| show standby brief                                  | Сводка по HSRP                                                 |
| debug standby ?                                     | Команды для дебага                                             |

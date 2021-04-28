# Оглавление
* [Пароли](#passwords)
* [Конфигурация SSH](#ssh)
* [Предварительная конфигурация](#preconfig)
* [VLANs](#vlans)
* [STP](#stp)
* [EtherChannel](#etherchannel)
* [HSRP](#hsrp)
* [DHCP](#dhcp)
* [DHCP/SLAAC](#dhcpv6)

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
| Команда                                                 | Описание                                                 |
| ------------------------------------------------------- | :------------------------------------------------------- |
| vlan \<vlan-id\>                                        | Создает vlan                                             |
| name \<vlan-name\>                                      | Задает имя vlan                                          |
| show vlan                                               | Полная информация по VLANs                               |
| show vlan brief                                         | Краткая сводка                                           |
| show vlan summary                                       | Общая информация по vlan                                 |
| show interface vlan \<vlan-id\>                         | Отображает информацию по конкретному vlan                |
| show interfaces \<interface-id\> switchport             | Отображает информацию vlan на выбранном интерфейсе       |
| show interfaces trunk                                   | Отображает информацию интерфейсов с режимом trunk        |
| switchport mode access                                  | Задает access-режим работы интерфейса                    |
| switchport mode trunk                                   | Задает trunk-режим работы интерфейса                     |
| switchport access vlan \<vlan-id\>                      | Задает vlan на конкретный интерфейс                      |
| switchport voice vlan \<vlan-id\>                       | Задает voice vlan                                        |
| switchport trunk native vlan \<vlan-id\>                | Задает native vlan (в режиме trunk)                      |
| switchport trunk allowed vlan \<vlan-id\>,\<vlan-id2\>  | Указывает список vlan разрешенных в режиме trunk         |
| switchport trunk allowed vlan add \<vlan-id\>           | Добавляет новый vlan в существующий список               |
| delete flash:vlan.dat                                   | Удаляет файл с конфигурацией VLANs                       |

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
| show spanning-tree vlan \<vlan-id\>                 | Информация по STP на конкретном VLAN                     |
| show spanning-tree summary                          | Статистика по STP и интерфейсам                          |
| spanning-tree mode \<proto\>                        | Задает протокол, "pvst" или "rapid-pvst"                 |
| spanning-tree cost \<1-200000000\>                  | Задает стоимость пути на интерфейсе                      |
| spanning-tree VLAN \<vlan-id\> root primary         | Задает vlan определенный приоритет в BID (root bridge)   |
| spanning-tree VLAN \<vlan-id\> root secondary       | Задает vlan определенный приоритет в BID (secondary)     |
| spanning-tree VLAN \<vlan-id\> priority \<value\>   | Задает vlan приоритет в BID (custom value)               |
| spanning-tree portfast                              | Включает PortFast на интерфейсе                          |
| spanning-tree bpduguard enable                      | Включает bpduguard                                       |
| spanning-tree link-type point-to-point              | Full-duplex                                              |
| spanning-tree link-type shared                      | Half-duplex                                              |

# <a name="etherchannel">EtherChannel
## Команды
| Команда                                             | Описание                                                       |
| --------------------------------------------------- | :------------------------------------------------------------- |
| channel-group \<id\> mode desirable                 | Создает агрегированный канал для выбранных интерфейсов (PAgP)  |
| channel-group \<id\> mode active                    | Создает агрегированный канал для выбранных интерфейсов (LACP)  |
| interface port-channel \<id\>                       | Настройка агрегированный канал                                 |
| show interfaces port-channel \<id\>                 | Отображает статус по агрегированному каналу                    |
| show etherchannel summary                           | Сводка по EtherChannel                                         |
| show etherchannel port-channel                      | Отображает информацию по агрегированному каналу                |
| show interfaces \<fid\> etherchannel                | Отображает роль выбранного интерфейса в EtherChannel           |

# <a name="hsrp">HSRP
## Команды
| Команда                                             | Описание                                                       |
| --------------------------------------------------- | :------------------------------------------------------------- |
| standby version \<version\>                         | Задает версию HSRP (выставлять на физическом интерфейсе)       |
| standby \<group-number\> ip \<ip\>                  | Задает виртуальный IP для выбранной группы                     |
| standby \<group-number\> priority \<value\>         | Задает приоритет маршрутизатору                                |
| standby \<group-number\> preempt                    | Задает приоритетное вытеснение                                 |
| show standby                                        | Информация по HSRP                                             |
| show standby brief                                  | Сводка по HSRP                                                 |
| debug standby ?                                     | Команды для дебага                                             |

# <a name="dhcp">DHCP
## Команды
| Команда                                             | Описание                                                       |
| --------------------------------------------------- | :------------------------------------------------------------- |
| ip dhcp excluded-address \<ip-start\> \<ip-end\>    | Исключает указанный диапазон из DHCP-пула                      |
| ip dhcp pool \<name\>                               | Создает DHCP-пул                                               |
| network \<network\> \<netmask\>                     | Задает подсеть для DHCP-пула                                   |
| default-router \<ip\>                               | Задает шлюз по умолчанию для DHCP-пула                         |
| dns-server \<ip\>                                   | Задает DNS для DHCP-пула                                       |
| domain-name \<name\>                                | Задает доменное имя для DHCP-пула                              |
| lease \<value\>                                     | Задает время жизни резервирования IP-адреса                    |
| show running-config \| section dhcp                 | Отображает конфигурацию DHCP                                   |
| show ip dhcp binding                                | Отображает биндинги IPv4 -> MAC службы DHCP                    |
| show ip dhcp server statistics                      | Отображает статистику службы DHCP                              |
| no service dhcp                                     | Отключает службу DHCP                                          |
| ip helper-address \<ip\>                            | Задает DHCP-relay                                              |

# <a name="dhcpv6">SLAAC/DHCPv6
## Stateless DHCPv6 Server
```shell
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 dhcp pool IPV6-STATELESS
R1(config-dhcpv6)# dns-server 2001:db8:acad:1::254
R1(config-dhcpv6)# domain-name example.com
R1(config-dhcpv6)# exit
R1(config)# interface GigabitEthernet0/0/1
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# ipv6 dhcp server IPV6-STATELESS
R1(config-if)# no shut
R1(config-if)# end
```

## Stateless DHCPv6 Client
```shell
R3(config)# ipv6 unicast-routing
R3(config)# interface g0/0/1
R3(config-if)# ipv6 enable
R3(config-if)# ipv6 address autoconfig
R3(config-if)# end
```

## Stateful DHCPv6 Server
```shell
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 dhcp pool IPV6-STATEFUL
R1(config-dhcpv6)# address prefix 2001:db8:acad:1::/64
R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
R1(config-dhcpv6)# domain-name example.com
R1(config)# interface GigabitEthernet0/0/1
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# ipv6 dhcp server IPV6-STATEFUL
R1(config-if)# no shut
R1(config-if)# end
```

## Stateful DHCPv6 Client
```shell
R3(config)# ipv6 unicast-routing
R3(config)# interface g0/0/1
R3(config-if)# ipv6 enable
R3(config-if)# ipv6 address dhcp
R3(config-if)# end
```

## DHCPv6 Relay Agent
```shell
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ipv6 dhcp relay destination 2001:db8:acad:1::2 G0/0/0
R1(config-if)# exit
```

## Команды
| Команда                                             | Описание                                                       |
| --------------------------------------------------- | :------------------------------------------------------------- |
| show ipv6 dhcp interface                            | Отображает информацию по DHCP на выбранном интерфейсе          |
| show ipv6 dhcp binding                              | Отображает DHCPv6 биндинги                                     |
| show ipv6 dhcp pool                                 | Отображает DHCPv6 пулы                                         |
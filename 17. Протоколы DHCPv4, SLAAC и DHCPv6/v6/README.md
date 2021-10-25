# Оглавление
* [Схема стенда](#scheme)
* [Таблица адресации](#table1)
* [Часть 1. Создание сети и настройка основных параметров устройства](#part1)
* [Часть 2. Проверка назначения адреса SLAAC от R1](#part2)
* [Часть 3. Настройка и проверка состояния DHCPv6 stateless сервера на R1](#part3)
* [Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1](#part4)
* [Часть 5. Настройка и проверка DHCPv6 Relay на R2](#part5)

# <a name="scheme"></a>Схема стенда
![](scheme.png)

# <a name="table1"></a>Таблица адресации
| Устройство    | Интерфейс          | IPv6-адрес             |
| ------------- | :----------------: | :--------------------: |
| R1            | G0/0/0             | 2001:db8:acad:2::1/64  |
|               |                    | fe80::1                |
|               | G0/0/1             | 2001:db8:acad:1::1/64  |
|               |                    | fe80::1                |
| R2            | G0/0/0             | 2001:db8:acad:2::2/64  |
|               |                    | fe80::2                |
|               | G0/0/1             | 2001:db8:acad:3::1/64  |
|               |                    | fe80::1                |
| PC-A          | NIC                | DHCP                   |
| PC-B          | NIC                | DHCP                   |

# <a name="part1"></a>Часть 1. Создание сети и настройка основных параметров устройства
## Создайте сеть согласно топологии
> Подключите устройства, как показано в топологии, и подсоедините необходимые кабели

Выполнено.

## Произведите базовую настройку маршрутизаторов

> Назначьте маршрутизатору имя устройства
> Активация IPv6-маршрутизации

```shell
Router(config)#hostname R1
R1(config)#ipv6 unicast-routing
```

```shell
Router(config)#hostname R2
R2(config)#ipv6 unicast-routing
```

## Настройка интерфейсов и маршрутизации для обоих маршрутизаторов

> Настройте интерфейсы G0/0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.
  Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0/0 на другом маршрутизаторе.

```shell
R1(config)#interface g0/0/0
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#no shutdown
R1(config-if)#interface g0/0/1
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#ipv6 route ::/0 g0/0/0 fe80::2
```

```shell
R2(config)#interface g0/0/0
R2(config-if)#ipv6 address 2001:db8:acad:2::2/64
R2(config-if)#ipv6 address fe80::2 link-local
R2(config-if)#no shutdown
R2(config-if)#interface g0/0/1
R2(config-if)#ipv6 address 2001:db8:acad:3::1/64
R2(config-if)#ipv6 address fe80::1 link-local
R2(config-if)#no shutdown
R2(config-if)#exit
R2(config)#ipv6 route ::/0 g0/0/0 fe80::1
```

> Убедитесь, что маршрутизация работает с помощью пинга адреса G0/0/1 R2 из R1

```shell
R1#ping 2001:db8:acad:3::1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:db8:acad:3::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```

# <a name="part2"></a>Часть 2. Проверка назначения адреса SLAAC от R1

> В части 2 вы убедитесь, что узел PC-A получает адрес IPv6 с помощью метода SLAAC.
  Включите PC-A и убедитесь, что сетевой адаптер настроен для автоматической настройки IPv6.
  Через несколько минут результаты команды ipconfig должны показать, что PC-A присвоил себе адрес из сети 2001:db8:1::/64.

```shell
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::260:2FFF:FE4A:6A1D
   IPv6 Address....................: 2001:DB8:ACAD:1:260:2FFF:FE4A:6A1D
   Autoconfiguration IPv4 Address..: 169.254.106.29
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
```

# <a name="part3"></a>Часть 3. Настройка и проверка состояния DHCPv6 stateless сервера на R1
## Более подробно изучите конфигурацию PC-A

> Выполните команду ipconfig /all на PC-A и посмотрите на результат

```shell
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0060.2F4A.6A1D
   Link-local IPv6 Address.........: FE80::260:2FFF:FE4A:6A1D
   IPv6 Address....................: 2001:DB8:ACAD:1:260:2FFF:FE4A:6A1D
   Autoconfiguration IP Address....: 169.254.106.29
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-72-81-08-E7-00-60-2F-4A-6A-1D
   DNS Servers.....................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: 
   Physical Address................: 0009.7CC6.D1BB
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-72-81-08-E7-00-60-2F-4A-6A-1D
   DNS Servers.....................: ::
                                     0.0.0.0
```

## Настройте R1 для предоставления DHCPv6 без состояния для PC-A

> Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS.
  В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad: :1, а имя домена — как stateless.com

```shell
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name stateless.com
```

> Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1
   и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса

```shell
R1(config)#interface g0/0/1
R1(config-if)#ipv6 nd other-config-flag
R1(config-if)#ipv6 dhcp server R1-STATELESS
```

> Перезапустите PC-A.
  Проверьте вывод ipconfig /all и обратите внимание на изменения.

```shell
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: stateless.com 
   Physical Address................: 0060.2F4A.6A1D
   Link-local IPv6 Address.........: FE80::260:2FFF:FE4A:6A1D
   IPv6 Address....................: 2001:DB8:ACAD:1:260:2FFF:FE4A:6A1D
   Autoconfiguration IP Address....: 169.254.106.29
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 677478929
   DHCPv6 Client DUID..............: 00-01-00-01-72-81-08-E7-00-60-2F-4A-6A-1D
   DNS Servers.....................: 2001:DB8:ACAD::254
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: stateless.com 
   Physical Address................: 0009.7CC6.D1BB
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 677478929
   DHCPv6 Client DUID..............: 00-01-00-01-72-81-08-E7-00-60-2F-4A-6A-1D
   DNS Servers.....................: ::
                                     0.0.0.0
```

> Тестирование подключения с помощью пинга IP-адреса интерфейса G0/1 R2

```shell
C:\>ping 2001:db8:acad:3::1

Pinging 2001:db8:acad:3::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254

Ping statistics for 2001:DB8:ACAD:3::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

# <a name="part4"></a>Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1

В части 4 настраивается R1 для ответа на запросы DHCPv6 от локальной сети на R2.

> Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. 
  Это предоставит адреса локальной сети, подключенной к интерфейсу G0/0/1 на R2. 
  В составе пула задайте DNS-сервер 2001:db8:acad: :254 и задайте доменное имя STATEFUL.com

```shell
R1(config)#ipv6 dhcp pool R2-STATEFUL
R1(config-dhcpv6)#address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name stateful.com
```

> Назначьте только что созданный пул DHCPv6 интерфейсу g0/0/0 на R1

```shell
R1(config)#interface g0/0/0
R1(config-if)#ipv6 dhcp server R2-STATEFUL
```

# <a name="part5"></a>Часть 5. Настройка и проверка DHCPv6 Relay на R2

В части 5 необходимо настроить и проверить ретрансляцию DHCPv6 на R2, позволяя PC-B получать адрес IPv6

## Включите PC-B и проверьте адрес SLAAC, который он генерирует

```shell
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0001.C914.1C7C
   Link-local IPv6 Address.........: FE80::201:C9FF:FE14:1C7C
   IPv6 Address....................: 2001:DB8:ACAD:3:201:C9FF:FE14:1C7C
   Autoconfiguration IP Address....: 169.254.28.124
   Subnet Mask.....................: 255.255.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-1C-B7-77-43-00-01-C9-14-1C-7C
   DNS Servers.....................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: 
   Physical Address................: 0007.EC05.609A
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-1C-B7-77-43-00-01-C9-14-1C-7C
   DNS Servers.....................: ::
                                     0.0.0.0
```

## Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1

> Настройте команду ipv6 dhcp relay на интерфейсе R2 G0/0/1, указав адрес назначения интерфейса G0/0/0 на R1.
  Также настройте команду managed-config-flag

По каким-то причинам Packet Tracer не поддерживает dhcp relay для ipv6
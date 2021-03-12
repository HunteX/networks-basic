# Схема стенда
![](scheme.png)

## Таблица адресации

| Устройство    | Интерфейс   | IP-адрес      | Маска подсети |
| :------------ |:----------- | :------------ | :------------ |
| S1            | VLAN 1      | 192.168.1.11  | 255.255.255.0 |
| S2            | VLAN 1      | 192.168.1.12  | 255.255.255.0 |
| PC-A          | NIC         | 192.168.1.1   | 255.255.255.0 |
| PC-B          | NIC         | 192.168.1.2   | 255.255.255.0 |

# Канальный уровень. Ethernet

## Часть 1. Создание и настройка сети

> Подключите сеть в соответствии с топологией.
> Настройте узлы ПК.
> Выполните инициализацию и перезагрузку коммутаторов.
> Настройте базовые параметры каждого коммутатора.

Выполнено.

>  1. Настройте имена устройств в соответствии с топологией.

```shell
Switch>enable
Switch#configure terminal
Switch(config)#hostname S1
S1(config)#
```

```shell
Switch>enable
Switch#configure terminal
Switch(config)#hostname S2
S2(config)#
```

>  2. Настройте IP-адреса, как указано в таблице адресации.

```shell
S1(config)#interface Vlan 1
S1(config-if)#ip address 192.168.1.11 255.255.255.0
S1(config-if)#no shutdown
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
```

```shell
S2#configure terminal 
S2(config)#interface Vlan 1
S2(config-if)#ip address 192.168.1.12 255.255.255.0
S2(config-if)#no shutdown 
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
```

>  3. Назначьте cisco в качестве паролей консоли и VTY.
>  4. Назначьте class в качестве пароля доступа к привилегированному режиму EXEC.

```shell
S1>enable
S1#configure terminal
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config)#enable secret class
S1(config)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config)#service password-encryption
S1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#reload
```

```shell
S2>enable
S2#configure terminal
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config)#enable secret class
S2(config)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config)#service password-encryption
S2#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
S2#reload
```

## Часть 2. Изучение таблицы МАС-адресов коммутатора

### Запишите МАС-адреса сетевых устройств.

> Откройте командную строку на PC-A и PC-B и введите команду ipconfig /all.
> Назовите физические адреса адаптера Ethernet.

> MAC-адрес компьютера PC-A:

`000C.CF55.41E5`

> MAC-адрес компьютера PC-B:

`000C.8587.49BE`

> Подключитесь к коммутаторам S1 и S2 через консоль и введите команду show interface F0/1 на каждом коммутаторе.
> Откройте окно конфигурации
> Назовите адреса оборудования во второй строке выходных данных команды (или зашитый адрес — bia).

> МАС-адрес коммутатора S1 Fast Ethernet 0/1:

`000d.bdae.2001`

> МАС-адрес коммутатора S2 Fast Ethernet 0/1:

`0060.3ee4.3101`

### Просмотрите таблицу МАС-адресов коммутатора

> Подключитесь к коммутатору S2 через консоль и просмотрите таблицу МАС-адресов до и после тестирования сетевой связи с помощью эхо-запросов.
> Подключитесь к коммутатору S2 через консоль и войдите в привилегированный режим EXEC.
> В привилегированном режиме EXEC введите команду show mac address-table и нажмите клавишу ввода.

```shell
S2#show mac address-table 
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    000d.bdae.2001    DYNAMIC     Fa0/1
```

### Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.

> В привилегированном режиме EXEC введите команду clear mac address-table dynamic и нажмите клавишу Enter

```shell
S2#clear mac address-table dynamic
S2#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
```

> Снова быстро введите команду show mac address-table.
> Указаны ли в таблице МАС-адресов адреса для VLAN 1? Указаны ли другие МАС-адреса?

Нет.

> Через 10 секунд введите команду show mac address-table и нажмите клавишу ввода. Появились ли в таблице МАС-адресов новые адреса?

```shell
S2#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    000d.bdae.2001    DYNAMIC     Fa0/1
```

Да, появился MAC-адрес устройства S1.

### С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

> На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.

```shell
C:\>arp -a
No ARP Entries Found
```

> Не считая адресов многоадресной и широковещательной рассылки, сколько пар IP- и МАС-адресов устройств было получено через протокол ARP?

На текущий момент ноль, так как взаимодействия между PC-B и S2 еще не было.

> Из командной строки PC-B отправьте эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2.

```shell
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.1.11

Pinging 192.168.1.11 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.11:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.1.12

Pinging 192.168.1.12 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.12:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

> От всех ли устройств получены ответы? Если нет, проверьте кабели и IP-конфигурации.

Да, ответы получены от всех устройств.

> Подключившись через консоль к коммутатору S2, введите команду show mac address-table.
> Добавил ли коммутатор в таблицу МАС-адресов дополнительные МАС-адреса? Если да, то какие адреса и устройства?

```shell
S2#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    000c.8587.49be    DYNAMIC     Fa0/18
   1    000c.cf55.41e5    DYNAMIC     Fa0/1
   1    000d.bdae.2001    DYNAMIC     Fa0/1
   1    00d0.ff9e.7362    DYNAMIC     Fa0/1
```

Учитывая следующую таблицу MAC-адресов:
* PC-A: `000C.CF55.41E5`
* PC-B: `000C.8587.49BE`
* S1 F0/1: `000d.bdae.2001`
* S2 F0/1: `0060.3ee4.3101`
* S1 Vlan 1: `00d0.ff9e.7362`

Получается, что помимо уже существующего адреса S1 F0/1 добавились MAC-адреса:
* PC-A
* PC-B
* S1 Vlan 1

> На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.

```shell
C:\>arp -a
  Internet Address      Physical Address      Type
  192.168.1.2           000c.8587.49be        dynamic
  192.168.1.11          00d0.ff9e.7362        dynamic
  192.168.1.12          00d0.587c.6a7c        dynamic
```

> Появились ли в ARP-кэше компьютера PC-B дополнительные записи для всех сетевых устройств, которым были отправлены эхо-запросы?

Да.

### Вопрос для повторения

> В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. Если компьютеров в сети немного, эта процедура выглядит достаточно простой. Какие сложности могут возникнуть в крупных сетях?

* Таблицы коммутаторов могут быть слишком большими.
* Широковещательные запросы будут получать все устройства в сети.
* Могут образовываться петли, что повлечет за собой нагрузку на коммутаторы и последующий их отказ.

# Оглавление
* [Схема стенда](#scheme)
* [Таблица адресации](#table1)
* [Таблица VLAN](#table2)
* [Часть 1. Создание сети и настройка основных параметров устройства](#part1)
* [Часть 2. Настройка и проверка двух серверов DHCPv4 на R1](#part2)
* [Часть 3. Настройка и проверка DHCP-ретрансляции на R2](#part3)

# <a name="scheme"></a>Схема стенда
![](scheme.png)

# <a name="table1"></a>Таблица адресации
| Устройство    | Интерфейс          | IP-адрес              | Маска подсети    | Шлюз по умолчанию |
| ------------- | :----------------: | :-------------------: | :--------------- | :---------------- |
| R1            | G0/0/0             | 10.0.0.1              | 255.255.255.252  | —                 |
|               | G0/0/1             | —                     | —                |                   |
|               | G0/0/1.100         |                       |                  |                   |
|               | G0/0/1.200         |                       |                  |                   |
|               | G0/0/1.1000        | —                     | —                |                   |
| R2            | G0/0/0             | 10.0.0.2              | 255.255.255.252  | —                 |
|               | G0/0/1             |                       |                  |                   |
| S1            | VLAN 200           |                       |                  |                   |
| S2            | VLAN 1             |                       |                  |                   |
| PC-A          | NIC                | DHCP                  | DHCP             | DHCP              |
| PC-B          | NIC                | DHCP                  | DHCP             | DHCP              |

# <a name="table2"></a>Таблица VLAN
| VLAN          | Имя                | Назначенный интерфейс                    |
| :------------ | :----------------- | :--------------------------------------- |
| 1             | Нет                | S2: F0/18                                |
| 100           | Клиенты            | S1: F0/6                                 |
| 200           | Управление         | S1: VLAN 200                             |
| 999           | Parking_Lot        | S1: F0/1-4, F0/7-24, G0/1-2              |
| 1000          | Собственная        | —                                        |

# <a name="part1"></a>Часть 1. Создание сети и настройка основных параметров устройства
## Создание схемы адресации. Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:
### Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).
> Подсеть A
> * Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100.
> * Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.

Для 58 хостов выделим подсеть 192.168.1.0/26.
Следовательно:
* Первый адрес хоста: 192.168.1.2
* Второй адрес хоста: 192.168.1.3
* Шлюз по умолчанию: 192.168.1.1

### Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1).
> Подсеть B
> * Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200.
> * Запишите второй IP-адрес в таблице адресов для S1 VLAN 1 и введите соответствующий шлюз по умолчанию.
Для 28 хостов выделим подсеть 192.168.1.64/27.
Следовательно:
* Первый адрес хоста: 192.168.1.66
* Второй адрес хоста: 192.168.1.67
* Шлюз по умолчанию: 192.168.1.65

### Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).
> Подсеть C
> * Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.
Для 12 хостов выделим подсеть 192.168.1.96/28.
Следовательно:
* Первый адрес хоста: 192.168.1.98
* Второй адрес хоста: 192.168.1.99
* Шлюз по умолчанию: 192.168.1.97

## Создайте сеть согласно топологии
Выполнено.

## Произведите базовую настройку маршрутизаторов
## Настройка маршрутизации между сетями VLAN на маршрутизаторе R1
## Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов
## Настройте базовые параметры каждого коммутатора
## Создайте сети VLAN на коммутаторе S1
## Назначьте сети VLAN соответствующим интерфейсам коммутатор
## Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q


# <a name="part2"></a>Часть 2. Настройка и проверка двух серверов DHCPv4 на R1
## Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей
## Сохраните конфигурацию
## Проверка конфигурации сервера DHCPv4
## Попытка получить IP-адрес от DHCP на PC-A


# <a name="part3"></a>Часть 3. Настройка и проверка DHCP-ретрансляции на R2
## Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1
## Попытка получить IP-адрес от DHCP на PC-B
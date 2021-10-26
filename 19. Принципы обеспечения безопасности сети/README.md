# Оглавление
* [Схема стенда](#scheme)
* [Таблица адресации](#table1)
* [Часть 1. Настройка основного сетевого устройства](#part1)
* [Часть 2. Настройка сетей VLAN](#part2)
* [Часть 3. Настройки безопасности коммутатора](#part3)

# <a name="scheme"></a>Схема стенда
![](scheme.png)

# <a name="table1"></a>Таблица адресации
| Устройство    | Интерфейс/vlan     | IP-адрес              | Маска подсети |
| ------------- | :----------------: | :-------------------: | :------------ |
| R1            | G0/0/1             | 192.168.10.1          | 255.255.255.0 |
|               | Loopback 0         | 10.10.1.1             | 255.255.255.0 |
| S1            | VLAN 10            | 192.168.10.201        | 255.255.255.0 |
| S2            | VLAN 10            | 192.168.10.202        | 255.255.255.0 |
| PC-A          | NIC                | DHCP                  | 255.255.255.0 |
| PC-B          | NIC                | DHCP                  | 255.255.255.0 |

# <a name="part1"></a>Часть 1. Настройка основного сетевого устройства
## Шаг 1.1. Создайте сеть.

> a. Создайте сеть согласно топологии.
> b. Инициализация устройств

Выполнено.

## Шаг 1.2. Настройте маршрутизатор R1

> 1.1.a. Загрузите следующий конфигурационный скрипт на R1

```shell
Router>enable
Router#configure terminal
Router(config)#hostname R1
R1(config)#no ip domain-lookup
R1(config)#ip dhcp excluded-address 192.168.10.1 192.168.10.9
R1(config)#ip dhcp excluded-address 192.168.10.201 192.168.10.202
R1(config)#ip dhcp pool Students
R1(dhcp-config)#network 192.168.10.0 255.255.255.0
R1(dhcp-config)#default-router 192.168.10.1
R1(dhcp-config)#domain-name ccna2.lab-11.6.1
R1(dhcp-config)#interface loopback0
R1(config-if)#ip address 10.10.1.1 255.255.255.0
R1(config)#interface g0/0/1
R1(config-if)#description Link to S1
R1(config-if)#ip address 192.168.10.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#line console 0
R1(config-line)#loggin sync
R1(config-line)#loggin synchronous 
R1(config-line)#exec-timeout 0 0
```

> 1.1.b. Проверьте текущую конфигурацию на R1, используя следующую команду:
R1# show ip interface brief

```shell
R1#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/1   192.168.10.1    YES manual up                    up 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Loopback0              10.10.1.1       YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
```

> c. Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up / up (при необходимости устраните неполадки).

Всё работает.

# <a name="part2"></a>Часть 2. Настройка сетей VLAN
# <a name="part3"></a>Часть 3. Настройки безопасности коммутатора

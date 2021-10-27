# Оглавление
* [Схема стенда](#scheme)
* [Таблица адресации](#table1)
* [Часть 1. Создание сети и настройка основных параметров устройства](#part1)
* [Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области](#part2)
* [Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области](#part3)

# <a name="scheme"></a>Схема стенда
![](scheme.png)

# <a name="table1"></a>Таблица адресации
| Устройство    | Интерфейс/vlan     | IP-адрес              | Маска подсети |
| ------------- | :----------------: | :-------------------: | :------------ |
| R1            | G0/0/1             | 10.53.0.1             | 255.255.255.0 |
|               | Loopback1          | 172.16.1.1            | 255.255.255.0 |
| R2            | G0/0/1             | 10.53.0.2             | 255.255.255.0 |
|               | Loopback1          | 192.168.1.1           | 255.255.255.0 |

# <a name="part1"></a>Часть 1. Создание сети и настройка основных параметров устройства
## Шаг 1.1. Создайте сеть согласно топологии

Выполнено.

## Шаг 1.2. Произведите базовую настройку маршрутизаторов

Выполнено.

## Шаг 1.3. Настройте базовые параметры каждого коммутатора

Выполнено.

# <a name="part2"></a>Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области
## Шаг 2.1. Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе
> 2.1.a. Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.

```shell
R1(config)#interface G0/0/1
R1(config-if)#ip address 10.53.0.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#interface loopback1
R1(config-if)#ip address 172.16.1.1 255.255.255.0
R1(config-if)#no shutdown
```

```shell
R2(config)#interface G0/0/1
R2(config-if)#ip address 10.53.0.2 255.255.255.0
R2(config-if)#no shutdown
R2(config-if)#interface loopback1
R2(config-if)#ip address 192.168.1.1 255.255.255.
R2(config-if)#no shutdown
```

> 2.1.b. Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.
> 2.1.c. Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).
> 2.1.d. Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.

```shell
R1(config)#router ospf 56
R1(config-router)#router-id 1.1.1.1
R1(config-router)#network 10.53.0.0 0.0.255.255 area 0
```

```shell
R2(config)#router ospf 56
R2(config-router)#router-id 2.2.2.2
R2(config-router)#network 10.53.0.0 0.0.255.255 area 0
```

> 2.1.e. Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.

```shell
R2(config-router)#network 192.168.1.0 0.0.0.255 area 0
```

> 2.1.f. Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.
Вопрос:
Какой маршрутизатор является DR? Какой маршрутизатор является BDR? Каковы критерии отбора?

Роль DR выполняет R2 так как у него выше router-id (priority у всех 1), и, соответственно, роль BDR у R1.

```shell
R1#show ip ospf neighbor 


Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/BDR        00:00:35    10.53.0.2       GigabitEthernet0/0/1
```

```shell
R2#show ip ospf neighbor 


Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/DR         00:00:39    10.53.0.1       GigabitEthernet0/0/1
```

> 1.1.a. На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. 
  Обратите внимание, что поведение OSPF по умолчанию заключается в объявлении интерфейса обратной связи 
 в качестве маршрута узла с использованием 32-битной маски.

```shell
R1#show ip route ospf
     192.168.1.0/32 is subnetted, 1 subnets
O       192.168.1.1 [110/2] via 10.53.0.2, 00:05:32, GigabitEthernet0/0/1
```

> 1.1.b. Запустите Ping до  адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным

```shell
R1#ping 192.168.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
```

# <a name="part3"></a>Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области
## Шаг 2.1. Реализация различных оптимизаций на каждом маршрутизаторе

> 2.1.a. На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.

```shell
R1(config)#interface g0/0/1
R1(config-if)#ip ospf priority 50
```

> 2.1.b. Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.

```shell
R1(config)#interface g0/0/1
R1(config-if)#ip ospf hello-interval 30
R1(config-if)#ip ospf dead-interval 120
```

```shell
R2(config)#interface g0/0/1
R2(config-if)#ip ospf hello-interval 30
R2(config-if)#ip ospf dead-interval 120
```

> 2.1.c. На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. 
  Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.

```shell
R1(config)#ip route 0.0.0.0 0.0.0.0 lo1
%Default route without gateway, if not a point-to-point interface, may impact performance
R1(config)#router ospf 56
R1(config-router)#default-information originate
```

> 2.1.d. добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка.
  Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.

```shell
R2(config-if)#interface lo1
R2(config-if)#ip ospf network point-to-point
```

```shell
R1#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.53.0.0/24 is directly connected, GigabitEthernet0/0/1
L       10.53.0.1/32 is directly connected, GigabitEthernet0/0/1
     172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
C       172.16.1.0/24 is directly connected, Loopback1
L       172.16.1.1/32 is directly connected, Loopback1
O    192.168.1.0/24 [110/2] via 10.53.0.2, 00:01:17, GigabitEthernet0/0/1
S*   0.0.0.0/0 is directly connected, Loopback1
```

> 2.1.e. Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.

```shell
R2(config)#router ospf 56
R2(config-router)#passive-interface lo1
```

> 2.1.f. Измените базовую пропускную способность для маршрутизаторов.
  После этой настройки перезапустите OSPF с помощью команды clear ip ospf process.
  Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.

```shell
R1(config)#router ospf 56
R1(config-router)#auto-cost reference-bandwidth 1000
% OSPF: Reference bandwidth is changed.
        Please ensure reference bandwidth is consistent across all routers.
```

```shell
R2(config)#router ospf 56
R2(config-router)#auto-cost reference-bandwidth 1000
```

## Шаг 2.2. Убедитесь, что оптимизация OSPFv2 реализовалась

> 2.2.a. Выполните команду show ip ospf interface g0/0/1 на R1 и убедитесь, 
  что приоритет интерфейса установлен равным 50, 
  а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast

Пришлось явно задать Dead Interval, потому что он не пересчитывался автоматически.
Вынес это в часть 2, туда же, где выставлял Hello Interval.

```shell
R1#show ip ospf interface g0/0/1

GigabitEthernet0/0/1 is up, line protocol is up
  Internet address is 10.53.0.1/24, Area 0
  Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 10
  Transmit Delay is 1 sec, State DR, Priority 50
  Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1
  Backup Designated Router (ID) 2.2.2.2, Interface address 10.53.0.2
  Timer intervals configured, Hello 30, Dead 120, Wait 120, Retransmit 5
    Hello due in 00:00:03
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 2.2.2.2  (Backup Designated Router)
  Suppress hello for 0 neighbor(s)
```

> 2.2.b. На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. 
  Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. 
  Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных.

Метрика с 1 увеличилась до 10.

```shell
R1#show ip route | include 192.168
O    192.168.1.0/24 [110/10] via 10.53.0.2, 00:05:07, GigabitEthernet0/0/1
```

> 2.2.c. Введите команду show ip route ospf на маршрутизаторе R2.
  Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1.

```shell
R2#show ip route ospf
O*E2 0.0.0.0/0 [110/1] via 10.53.0.1, 01:18:09, GigabitEthernet0/0/1
```

> 2.2.d. Запустите Ping до адреса интерфейса R1 Loopback 1 из R2. Выполнение команды ping должно быть успешным.
Вопрос:
Почему стоимость OSPF для маршрута по умолчанию отличается от стоимости OSPF в R1 для сети 192.168.1.0/24?

Стоимость у маршрутов отличается потому что для E2 маршрутов она не меняется и равна 1.

```shell
R2#ping 172.16.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
```

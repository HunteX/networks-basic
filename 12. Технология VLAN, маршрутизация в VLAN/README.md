# Оглавление
* [Схема стенда](#scheme)
* [Таблица адресации](#table1)
* [Таблица VLAN](#table2)
* [Часть 1. Создание сети и настройка основных параметров устройства](#part1)
* [Часть 2. Создание сетей VLAN и назначение портов коммутатора](#part2)
* [Часть 3. Настройка транка 802.1Q между коммутаторами](#part3)
* [Часть 4. Настройка маршрутизации между сетями VLAN](#part4)
* [Часть 5. Проверка, что маршрутизация между VLAN работает](#part5)

# <a name="scheme"></a>Схема стенда
![](scheme.png)

# <a name="table1"></a>Таблица адресации

| Устройство    | Интерфейс          | IP-адрес              | Маска подсети  | Шлюз по умолчанию |
| ------------- |:-----------------: | :-------------------: | :------------- | :---------------- |
| R1            | G0/0/1.10          |  192.168.10.1         | 255.255.255.0  | -                 |
|               | G0/0/1.20          |  192.168.20.1         | 255.255.255.0  | -                 |
|               | G0/0/1.30          |  192.168.30.1         | 255.255.255.0  | -                 |
|               | G0/0/1.1000        |  -                    | -              | -                 |
| S1            | VLAN 10            |  192.168.10.11        | 255.255.255.0  | 192.168.10.1      |
| S2            | VLAN 10            |  192.168.10.12        | 255.255.255.0  | 192.168.10.1      |
| PC-A          | NIC                |  192.168.20.3         | 255.255.255.0  | 192.168.20.1      |
| PC-B          | NIC                |  192.168.30.3         | 255.255.255.0  | 192.168.30.1      |

# <a name="table2"></a>Таблица VLAN

| VLAN          | Имя                | Назначенный интерфейс                                         |
| ------------- |: ---------------- :| :------------------------------------------------------------ |
| 10            | Управление         | S1: VLAN 10<br>S2: VLAN 10                                    |
| 20            | Sales              | S1: F0/6                                                      |
| 30            | Operations         | S2: F0/18                                                     |
| 999           | Parking_Lot        | C1: F0/2-4, F0/7-24, G0/1-2<br>C2: F0/2-17, F0/19-24, G0/1-2  |
| 1000          | Собственная        | -                                                             |

# <a name="part1"></a>Часть 1. Создание сети и настройка основных параметров устройства

## Создайте сеть согласно топологии
Готово

## Настройте базовые параметры для маршрутизатора.
> Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
> Откройте окно конфигурации
> Войдите в режим конфигурации.
> Назначьте маршрутизатору имя устройства.
> Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
> Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
> Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
> Установите cisco в качестве пароля виртуального терминала и активируйте вход.
> Зашифруйте открытые пароли.
> Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
> Сохраните текущую конфигурацию в файл загрузочной конфигурации.
> Настройте на маршрутизаторе время

```shell
Router>enable
Router#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#no ip domain-lookup
R1(config)#enable secret class
R1(config)#line console 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 15
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption
R1(config)#banner motd #Authorized Access Only!#
R1(config)#exit
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#clock set 12:14:00 18 April 2021
```

## Настройте базовые параметры каждого коммутатора.
> Присвойте коммутатору имя устройства.
> Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
> Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
> Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
> Установите cisco в качестве пароля виртуального терминала и активируйте вход.
> Зашифруйте открытые пароли.
> Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
> Настройте на коммутаторах время.
> Сохранение текущей конфигурации в качестве начальной.

```shell
Switch>enable
Switch#configure terminal
Switch(config)#hostname S1
S1(config)#no ip domain-lookup
S1(config)#enable secret class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption
S1(config)#banner motd #Authorized Access Only!#
S1(config)#exit
S1#clock set 12:19:00 18 April 2021
S1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
```

```shell
Switch>enable
Switch#configure terminal
Switch(config)#hostname S2
S2(config)#no ip domain-lookup
S2(config)#enable secret class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 15
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption
S2(config)#banner motd #Authorized Access Only!#
S2(config)#exit
S2#clock set 12:23:00 18 April 2021
S2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
```

## Настройте узлы ПК
Готово

# <a name="part2"></a>Часть 2. Создание сетей VLAN и назначение портов коммутатора

# <a name="part3"></a>Часть 3. Настройка транка 802.1Q между коммутаторами

# <a name="part4"></a>Часть 4. Настройка маршрутизации между сетями VLAN

# <a name="part5"></a>Часть 5. Проверка, что маршрутизация между VLAN работает

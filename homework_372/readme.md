### 1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
Проверка в Windows:
```
PS F:\vagrant> ipconfig

Настройка протокола IP для Windows


Адаптер Ethernet Ethernet 2:

   Состояние среды. . . . . . . . : Среда передачи недоступна.
   DNS-суффикс подключения . . . . . :

Адаптер Ethernet Ethernet 3:

   Состояние среды. . . . . . . . : Среда передачи недоступна.
   DNS-суффикс подключения . . . . . :

Адаптер Ethernet vEthernet (vComm1):

   DNS-суффикс подключения . . . . . : mood51.loc
   Локальный IPv6-адрес канала . . . : fe80::5899:f2f6:da82:7869%21
   IPv4-адрес. . . . . . . . . . . . : 192.168.1.9
   Маска подсети . . . . . . . . . . : 255.255.255.192
   Основной шлюз. . . . . . . . . : 192.168.1.1

Адаптер Ethernet VirtualBox Host-Only Network:

   DNS-суффикс подключения . . . . . :
   Локальный IPv6-адрес канала . . . : fe80::1c66:c21c:1e96:ac8f%19
   IPv4-адрес. . . . . . . . . . . . : 192.168.56.1
   Маска подсети . . . . . . . . . . : 255.255.255.0
   Основной шлюз. . . . . . . . . :

Адаптер Ethernet vEthernet (Default Switch):

   DNS-суффикс подключения . . . . . :
   Локальный IPv6-адрес канала . . . : fe80::805f:8ca2:ecd8:f64a%33
   IPv4-адрес. . . . . . . . . . . . : 192.168.122.17
   Маска подсети . . . . . . . . . . : 255.255.255.240
   Основной шлюз. . . . . . . . . :
```
Проверка в Linux:
Состояние интерфейсов:
```
vagrant@vagrant:~$ ip link
vagrant@vagrant:~$ ip -c -br link
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>
eth0             UP             08:00:27:73:60:cf <BROADCAST,MULTICAST,UP,LOWER_UP>
```
Просмотр адресов:
```
vagrant@vagrant:~$ ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic eth0
       valid_lft 85701sec preferred_lft 85701sec
    inet6 fe80::a00:27ff:fe73:60cf/64 scope link
       valid_lft forever preferred_lft forever
```
### 2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
Ответ: используте протокол LLDP. В linux имеется пакет lldpd.
Пример выполнения команды:
```
root@raspberrypi:/home/pi# lldpctl
-------------------------------------------------------------------------------
LLDP neighbors:
-------------------------------------------------------------------------------
Interface:    eth0, via: LLDP, RID: 1, Time: 0 day, 00:04:51
  Chassis:
    ChassisID:    mac 00:ad:24:8c:6d:28
  Port:
    PortID:       local eth1/0/28
    TTL:          120
-------------------------------------------------------------------------------
```
### 3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
Ответ: используется технология VLAN. Для примера сделал настройку на имеющимся в распоряжении микрокомпьтере Raspberry Pi, покдлюченному к коммутатору L2+ DLing 1510-52, порт которого установлен в режим hybrid.
Установка vlan:
```
root@raspberrypi:/home/pi# apt install vlan
```
Создаем конфиг, добавляем vlan300:
```
sudo nano /etc/network/interfaces.d/vlans
auto eth0.300
iface eth0.300 inet manual
  vlan-raw-device eth0
root@raspberrypi: reboot
```
Перезагрузка
```
root@raspberrypi:/home/pi# ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether dc:a6:32:2a:b4:55 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.7/26 brd 192.168.1.63 scope global dynamic noprefixroute eth0
       valid_lft 690772sec preferred_lft 604372sec
    inet6 fe80::7940:33b8:ca96:54e7/64 scope link
       valid_lft forever preferred_lft forever
4: eth0.300@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether dc:a6:32:2a:b4:55 brd ff:ff:ff:ff:ff:ff
    inet 192.168.30.156/23 brd 192.168.31.255 scope global dynamic noprefixroute eth0.300
       valid_lft 3177sec preferred_lft 2727sec
    inet6 fe80::b88a:5da1:7d4f:fb86/64 scope link
       valid_lft forever preferred_lft forever
```

### 4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
Типы агрегации интерфейсов в Linux:

Mode-0(balance-rr) – Данный режим используется по умолчанию. Balance-rr обеспечивается балансировку нагрузки и отказоустойчивость. В данном режиме сетевые пакеты отправляются “по кругу”, от первого интерфейса к последнему. Если выходят из строя интерфейсы, пакеты отправляются на остальные оставшиеся. Дополнительной настройки коммутатора не требуется при нахождении портов в одном коммутаторе. При разностных коммутаторах требуется дополнительная настройка.

Mode-1(active-backup) – Один из интерфейсов работает в активном режиме, остальные в ожидающем. При обнаружении проблемы на активном интерфейсе производится переключение на ожидающий интерфейс. Не требуется поддержки от коммутатора.

Mode-2(balance-xor) – Передача пакетов распределяется по типу входящего и исходящего трафика по формуле ((MAC src) XOR (MAC dest)) % число интерфейсов. Режим дает балансировку нагрузки и отказоустойчивость. Не требуется дополнительной настройки коммутатора/коммутаторов.

Mode-3(broadcast) – Происходит передача во все объединенные интерфейсы, тем самым обеспечивая отказоустойчивость. Рекомендуется только для использования MULTICAST трафика.

Mode-4(802.3ad) – динамическое объединение одинаковых портов. В данном режиме можно значительно увеличить пропускную способность входящего так и исходящего трафика. Для данного режима необходима поддержка и настройка коммутатора/коммутаторов.

Mode-5(balance-tlb) – Адаптивная балансировки нагрузки трафика. Входящий трафик получается только активным интерфейсом, исходящий распределяется в зависимости от текущей загрузки канала каждого интерфейса. Не требуется специальной поддержки и настройки коммутатора/коммутаторов.

Mode-6(balance-alb) – Адаптивная балансировка нагрузки. Отличается более совершенным алгоритмом балансировки нагрузки чем Mode-5). Обеспечивается балансировку нагрузки как исходящего так и входящего трафика. Не требуется специальной поддержки и настройки коммутатора/коммутаторов.

Методы балансировки:
 - по MAC-адресу отправителя или MAC-адресу получателя или учитывая оба адреса
 - по IP-адресу отправителя или IP-адресу получателя или учитывая оба адреса
 - по номеру порта отправителя или номеру порта получателя или учитывая оба порта
 
 Пример конфигурации файла /etc/network/interfaces 
 ```
 auto bond0

iface bond0 inet static
    address 10.31.1.5
    netmask 255.255.255.0
    network 10.31.1.0
    gateway 10.31.1.254
    bond-slaves eth0 eth1
    bond-mode active-backup
    bond-miimon 100
    bond-downdelay 200
    bond-updelay 200
 ```
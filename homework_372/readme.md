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

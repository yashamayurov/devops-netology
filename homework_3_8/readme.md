#### 1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP
```
route-views>show ip route 83.171.107.190/32
                                        ^
% Invalid input detected at '^' marker.
route-views>show ip route 83.171.107.190
Routing entry for 83.171.96.0/19
  Known via "bgp 6447", distance 20, metric 0
  Tag 6939, type external
  Last update from 64.71.137.241 3d18h ago
  Routing Descriptor Blocks:
  * 64.71.137.241, from 64.71.137.241, 3d18h ago
      Route metric is 0, traffic share count is 1
      AS Hops 2
      Route tag 6939
      MPLS label: none
```

```
route-views>show bgp 83.171.107.190
BGP routing table entry for 83.171.96.0/19, version 1407109813
Paths: (23 available, best #22, table default)
  Not advertised to any peer
  Refresh Epoch 1
  3356 12389, (aggregated by 12389 212.48.198.56)
    4.68.4.46 from 4.68.4.46 (4.69.184.201)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 3356:2 3356:22 3356:100 3356:123 3356:501 3356:901 3356:2065
      path 7FE0202940C0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3549 3356 12389, (aggregated by 12389 212.48.198.56)
    208.51.134.254 from 208.51.134.254 (67.16.168.191)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 3356:2 3356:22 3356:100 3356:123 3356:501 3356:901 3356:2065 3549:2581 3549:30840
      path 7FE15FB76E00 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  53767 14315 6453 6453 3356 12389, (aggregated by 12389 212.48.198.56)
    162.251.163.2 from 162.251.163.2 (162.251.162.3)
      Origin IGP, localpref 100, valid, external
      Community: 14315:5000 53767:5000
      path 7FE080B801C0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  57866 3356 12389, (aggregated by 12389 212.48.198.56)
    37.139.139.17 from 37.139.139.17 (37.139.139.17)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 3356:2 3356:22 3356:100 3356:123 3356:501 3356:901 3356:2065
      path 7FE1731AC838 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  20130 6939 12389, (aggregated by 12389 212.48.198.56)
    140.192.8.16 from 140.192.8.16 (140.192.8.16)
      Origin IGP, localpref 100, valid, external
      path 7FE0CB3BAF88 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  101 3356 12389, (aggregated by 12389 212.48.198.56)
    209.124.176.223 from 209.124.176.223 (209.124.176.223)
      Origin IGP, localpref 100, valid, external
      Community: 101:20100 101:20110 101:22100 3356:2 3356:22 3356:100 3356:123 3356:501 3356:901 3356:2065
      Extended Community: RT:101:22100
      path 7FE04E5A3A58 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  852 3356 12389, (aggregated by 12389 212.48.198.56)
    154.11.12.212 from 154.11.12.212 (96.1.209.43)
      Origin IGP, metric 0, localpref 100, valid, external
      path 7FE157E31AF0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3333 1103 12389, (aggregated by 12389 212.48.198.56)
    193.0.0.56 from 193.0.0.56 (193.0.0.56)
      Origin IGP, localpref 100, valid, external
      path 7FE10B05B238 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  2497 12389, (aggregated by 12389 212.48.198.56)
    202.232.0.2 from 202.232.0.2 (58.138.96.254)
      Origin IGP, localpref 100, valid, external
      path 7FE10D1AAA58 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  20912 3257 3356 12389, (aggregated by 12389 212.48.198.56)
    212.66.96.126 from 212.66.96.126 (212.66.96.126)
      Origin IGP, localpref 100, valid, external
      Community: 3257:8070 3257:30515 3257:50001 3257:53900 3257:53902 20912:65004
      path 7FE170C5CDC8 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 3
  3303 12389, (aggregated by 12389 212.48.198.56)
    217.192.89.50 from 217.192.89.50 (138.187.128.158)
      Origin IGP, localpref 100, valid, external
      Community: 3303:1004 3303:1006 3303:1030 3303:3056
      path 7FE11282E528 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  4901 6079 3356 12389, (aggregated by 12389 212.48.198.56)
    162.250.137.254 from 162.250.137.254 (162.250.137.254)
      Origin IGP, localpref 100, valid, external
      Community: 65000:10100 65000:10300 65000:10400
      path 7FE12855BE38 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  7660 2516 12389, (aggregated by 12389 212.48.198.56)
    203.181.248.168 from 203.181.248.168 (203.181.248.168)
      Origin IGP, localpref 100, valid, external
      Community: 2516:1050 7660:9001
      path 7FE08E1953C8 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  7018 1299 12389, (aggregated by 12389 212.48.198.56)
    12.0.1.63 from 12.0.1.63 (12.0.1.63)
      Origin IGP, localpref 100, valid, external
      Community: 7018:5000 7018:37232
      path 7FE153DBAA28 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  49788 12552 12389, (aggregated by 12389 212.48.198.56)
    91.218.184.60 from 91.218.184.60 (91.218.184.60)
      Origin IGP, localpref 100, valid, external
      Community: 12552:12000 12552:12100 12552:12101 12552:22000
      Extended Community: 0x43:100:1
      path 7FE16A95AF68 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  8283 1299 12389, (aggregated by 12389 212.48.198.56)
    94.142.247.3 from 94.142.247.3 (94.142.247.3)
      Origin IGP, metric 0, localpref 100, valid, external
      Community: 1299:30000 8283:1 8283:101 8283:103
      unknown transitive attribute: flag 0xE0 type 0x20 length 0x24
        value 0000 205B 0000 0000 0000 0001 0000 205B
              0000 0005 0000 0001 0000 205B 0000 0005
              0000 0003
      path 7FE047F363B8 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  1221 4637 12389, (aggregated by 12389 212.48.198.56)
    203.62.252.83 from 203.62.252.83 (203.62.252.83)
      Origin IGP, localpref 100, valid, external
      path 7FE109C7CAD0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  701 1273 12389, (aggregated by 12389 212.48.198.56)
    137.39.3.55 from 137.39.3.55 (137.39.3.55)
      Origin IGP, localpref 100, valid, external
      path 7FE15EE03F50 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3257 1299 12389, (aggregated by 12389 212.48.198.56)
    89.149.178.10 from 89.149.178.10 (213.200.83.26)
      Origin IGP, metric 10, localpref 100, valid, external
      Community: 3257:8794 3257:30052 3257:50001 3257:54900 3257:54901
      path 7FE0C7761808 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  3561 3910 3356 12389, (aggregated by 12389 212.48.198.56)
    206.24.210.80 from 206.24.210.80 (206.24.210.80)
      Origin IGP, localpref 100, valid, external
      path 7FE0B497C6F0 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  1351 6939 12389, (aggregated by 12389 212.48.198.56)
    132.198.255.253 from 132.198.255.253 (132.198.255.253)
      Origin IGP, localpref 100, valid, external
      path 7FE0B5274EA8 RPKI State valid
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  6939 12389, (aggregated by 12389 212.48.198.56)
    64.71.137.241 from 64.71.137.241 (216.218.252.164)
      Origin IGP, localpref 100, valid, external, best
      path 7FE13A598B50 RPKI State valid
      rx pathid: 0, tx pathid: 0x0
  Refresh Epoch 1
  19214 3257 1299 12389, (aggregated by 12389 212.48.198.56)
    208.74.64.40 from 208.74.64.40 (208.74.64.40)
      Origin IGP, localpref 100, valid, external
      Community: 3257:8108 3257:30391 3257:50002 3257:51200 3257:51203
      path 7FE04C6A42F0 RPKI State valid
      rx pathid: 0, tx pathid: 0
      
```
#### 2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.
Создание интерфейса:
```
root@vagrant:~# echo "dummy" >> /etc/modules
root@vagrant:~# echo "options dummy numdummies=2" > /etc/modprobe.d/dummy.conf
root@vagrant:~# nano /etc/network/interfaces
```
Содержимое /etc/network/interfaces
```
auto dummy0
iface dummy0 inet static
 address 10.2.2.2/32
 pre-up ip link add dummy0 type dummy
 post-down ip link del dummy0
```
Перезапуск сети:
```
root@vagrant:~# /etc/init.d/networking restart
Restarting networking (via systemctl): networking.service.
```
Добавление временных маршрутов:
```
root@vagrant:~# ip route add 10.0.0.0/24 via 10.0.2.2
root@vagrant:~# ip route add 10.0.1.0/24 via 10.0.2.2
root@vagrant:~# ip route add 10.0.1.0/27 via 10.0.2.15
```
Просмотр таблицы маршрутизации:
```
root@vagrant:~# ip route show
default via 10.0.2.2 dev eth0 proto dhcp src 10.0.2.15 metric 100
10.0.0.0/24 via 10.0.2.2 dev eth0
10.0.1.0/27 via 10.0.2.15 dev eth0
10.0.1.0/24 via 10.0.2.2 dev eth0
10.0.2.0/24 dev eth0 proto kernel scope link src 10.0.2.15
10.0.2.2 dev eth0 proto dhcp scope link src 10.0.2.15 metric 100
```

#### 3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров
Выполним команду ss -ltn -p, из вывода которой видно, что прослушиваются порты 111,53,22 и процессы, прослушивающие порты.
```
root@vagrant:~# ss -ltn -p
State             Recv-Q             Send-Q                         Local Address:Port                         Peer Address:Port            Process
LISTEN            0                  4096                                 0.0.0.0:111                               0.0.0.0:*                users:(("rpcbind",pid=554,fd=4),("systemd",pid=1,fd=35))
LISTEN            0                  4096                           127.0.0.53%lo:53                                0.0.0.0:*                users:(("systemd-resolve",pid=555,fd=13))
LISTEN            0                  128                                  0.0.0.0:22                                0.0.0.0:*                users:(("sshd",pid=3595,fd=3))
LISTEN            0                  4096                                    [::]:111                                  [::]:*                users:(("rpcbind",pid=554,fd=6),("systemd",pid=1,fd=37))
LISTEN            0                  128                                     [::]:22                                   [::]:*                users:(("sshd",pid=3595,fd=4))
```
Порт 22 - SSH-сервер
Порт 53 - DNS
#### 4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?

```
root@vagrant:~# ss -uap
State             Recv-Q            Send-Q                        Local Address:Port                           Peer Address:Port            Process
UNCONN            0                 0                             127.0.0.53%lo:domain                              0.0.0.0:*                users:(("systemd-resolve",pid=555,fd=12))
UNCONN            0                 0                            10.0.2.15%eth0:bootpc                              0.0.0.0:*                users:(("systemd-network",pid=400,fd=19))
UNCONN            0                 0                                   0.0.0.0:sunrpc                              0.0.0.0:*                users:(("rpcbind",pid=554,fd=5),("systemd",pid=1,fd=36))
UNCONN            0                 0                                      [::]:sunrpc                                 [::]:*                users:(("rpcbind",pid=554,fd=7),("systemd",pid=1,fd=38))
```
#### 5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.

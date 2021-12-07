### 1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:
поместите его в автозагрузку,
предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
Сервис установлен.
##### Конфигурационный файл сервиса:
```
vagrant@vagrant:~$ sudo nano /etc/systemd/system/node_exporter.service
[Unit]
Description=Node Exporter

[Service]
ExecStart=/usr/local/bin/node_exporter
EnvironmentFile=/etc/default/node_exporter


[Install]
WantedBy=default.target
```
#### Опции запуска возможно передать при помощи файла, указаннгого в параметре EnvironmentFile

```
vagrant@vagrant:~$ sudo systemctl status node_exporter
###### 
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2021-12-04 18:39:43 UTC; 6min ago
   Main PID: 1410 (node_exporter)
      Tasks: 4 (limit: 1071)
     Memory: 2.4M
     CGroup: /system.slice/node_exporter.service
             └─1410 /usr/local/bin/node_exporter

Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:115 level=info collector=thermal_zone
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:115 level=info collector=time
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:115 level=info collector=timex
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:115 level=info collector=udp_queues
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:115 level=info collector=uname
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:115 level=info collector=vmstat
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:115 level=info collector=xfs
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:115 level=info collector=zfs
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
Dec 04 18:39:43 vagrant node_exporter[1410]: ts=2021-12-04T18:39:43.862Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
vagrant@vagrant:~$ sudo reboot
##### Перезагрузка
vagrant@vagrant:~$ sudo systemctl status node_exporter
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2021-12-04 18:48:03 UTC; 47s ago
   Main PID: 584 (node_exporter)
      Tasks: 4 (limit: 1071)
     Memory: 13.9M
     CGroup: /system.slice/node_exporter.service
             └─584 /usr/local/bin/node_exporter

Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.336Z caller=node_exporter.go:115 level=info collector=thermal_zone
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.336Z caller=node_exporter.go:115 level=info collector=time
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.336Z caller=node_exporter.go:115 level=info collector=timex
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.336Z caller=node_exporter.go:115 level=info collector=udp_queues
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.337Z caller=node_exporter.go:115 level=info collector=uname
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.337Z caller=node_exporter.go:115 level=info collector=vmstat
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.337Z caller=node_exporter.go:115 level=info collector=xfs
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.337Z caller=node_exporter.go:115 level=info collector=zfs
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.338Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
Dec 04 18:48:04 vagrant node_exporter[584]: ts=2021-12-04T18:48:04.340Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
```
### 2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.

#### CPU
node_cpu_seconds_total{cpu="0",mode="idle"} 675.13
node_cpu_seconds_total{cpu="0",mode="system"} 5.18
node_cpu_seconds_total{cpu="0",mode="user"} 1.54
node_cpu_seconds_total{cpu="1",mode="idle"} 669.78
node_cpu_seconds_total{cpu="1",mode="system"} 4.54
node_cpu_seconds_total{cpu="1",mode="user"} 0.64
#### Память 
node_memory_MemAvailable_bytes 7.72186112e+08
node_memory_MemFree_bytes 4.76033024e+08
#### Диск
node_disk_read_time_seconds_total{device="sda"} 12.897
node_disk_write_time_seconds_total{device="sda"} 2.5100000000000002
#### Сеть
node_network_receive_bytes_total{device="eth0"} 920622
node_network_receive_errs_total{device="eth0"} 0
node_network_transmit_bytes_total{device="eth0"} 23612
node_network_transmit_errs_total{device="eth0"} 0

### 3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). После успешной установки:
#### в конфигурационном файле /etc/netdata/netdata.conf в секции [web] замените значение с localhost на bind to = 0.0.0.0
открываю файл 
vagrant@vagrant:~ $ sudo nano /etc/netdata/netdata.conf
Вижу, что секция [WEB] отсутствует, согласно комментарию загружаю последнюю версию командой:
wget -O /etc/netdata/netdata.conf http://localhost:19999/netdata.conf

vagrant@vagrant:~ $ sudo nano /etc/netdata/netdata.conf

Секция [WEB] появилась отредатировал.

Отредактировал vagrantfile

Перешел на вкладку Windows Terminal открытую в папке где находться виртуальная машина:

vagrant@vagrant:~ $ sudo nano /etc/netdata/netdata.conf

Виртуальая машина перезагрузилась. Открыл браузер ввел в адресной строке http://localhost:19999/. Открылось отображение графиков со значениям метрик.

### 4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
Для этого можно посмотреть информацию DMI (Desktop Management Interface) — программный интерфейс (API), позволяющий программному обеспечению собирать данные о характеристиках аппаратуры компьютера:
vagrant@vagrant:~ $ dmesg | grep DMI

[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006

[    0.296648] ACPI: Added _OSI(Linux-Lenovo-NV-HDMI-Audio)

Строка innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006 свидетельсвует о том, что ОС запущена на гипервизоре VirtualBox.

### 5. Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?
Максисмальное количество открытах дескрипторов для пользователя

vagrant@vagrant:~$ /sbin/sysctl -n fs.nr_open
1048576

Можно изменить при помощи команд:
vagrant@vagrant:~ $ ulimit -Sn
1024
мягкий лимит на пользователя (Мягкий предел - это то, что фактически применяется для сеанса или процесса)
vagrant@vagrant:~ $ ulimit -Hn
1048576

жесткий лимит на пользователя (Жесткий предел - это потолок для мягкого ограничения.)

### 6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.
vagrant@vagrant:~ $ sudo -i
root@vagrant:~# sleep 1h
^Z

[1]+  Stopped                 sleep 1h
root@vagrant: ~# bg

[1]+ sleep 1h &

root@vagrant: ~# ps -e | grep sleep

   1176 pts/0    00:00:00 sleep
   
root@vagrant: ~# sudo nsenter --target 1176 --pid --mount --no-fork

root@vagrant:/# ps

    PID TTY          TIME CMD
    
   1165 pts/0    00:00:00 sudo 
   
   1167 pts/0    00:00:00 bash
   
   1176 pts/0    00:00:00 sleep
   
   1183 pts/0    00:00:00 sudo
   
   1184 pts/0    00:00:00 bash
   
   1193 pts/0    00:00:00 ps
   
root@vagrant:/#

### 7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
Это определяет функцию с именем : , которая вызывает себя дважды (Код: : | : ). Это происходит в фоновом режиме ( & ). После ; определение функции выполнено, и функция : запускается.

Таким образом, каждый экземпляр : начинает два новых : и так далее...


### 1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:
поместите его в автозагрузку,
предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
Сервис установлен.

##### Конфигурационный файл сервиса (Опции запуска возможно передать при помощи файла, указаннгого в параметре EnvironmentFile):
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
```
##### Перезагрузка
```
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

### 7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
Это определяет функцию с именем : , которая вызывает себя дважды (Код: : | : ). Это происходит в фоновом режиме ( & ). После ; определение функции выполнено, и функция : запускается.

Таким образом, каждый экземпляр : начинает два новых : и так далее...
Судя по выводу 
```
root@vagrant:/# dmesg
[ 1217.300870] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-1.scope
```
Cистема на основании этих файлов в пользовательской зоне ресурсов имеет определенное ограничение на создаваемые ресурси и соответсвенно при превышении начинает блокировать создание большего числа процессов.

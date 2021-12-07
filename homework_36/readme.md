### 1. Работа c HTTP через телнет.
```
vagrant@vagrant:~$ telnet stackoverflow.com 80
Trying 151.101.129.69...
Connected to stackoverflow.com.
Escape character is '^]'.
GET /questions HTTP/1.0
HOST: stackoverflow.com

HTTP/1.0 301 Moved Permanently
Connection: Close
X-Tracking-Ref: <0.19519.3581>
X-Execution-Time: 158763
Cache-Control: no-cache, no-store, must-revalidate
Location: https://stackoverflow.com/questions
x-request-guid: 1c83ce28-7e54-4466-92e5-c8ca46a8ba6c
feature-policy: microphone 'none'; speaker 'none'
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
Date: Tue, 07 Dec 2021 10:12:27 GMT
Via: 1.1 varnish
X-Served-By: cache-fra19163-FRA
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1638871948.702112,VS0,VE92
Vary: Fastly-SSL
X-DNS-Prefetch-Control: off
Set-Cookie: prov=659601c4-ebdc-08ff-260f-6fecaa84eadc; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly


Connection closed by foreign host.
```
В ответ возвращается код 301, означающий что страница перемещена по адресу указанным в поле: https://stackoverflow.com/questions
### 2. Повторите задание 1 в браузере, используя консоль разработчика F12
##### найдите первый ответ HTTP сервера, откройте вкладку Headers
##### укажите в ответе полученный HTTP код.
Status Code: 307 Internal Redirect
##### проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?
https://stackoverflow.com/  351 мс
##### приложите скриншот консоли браузера в ответ.
![image](https://user-images.githubusercontent.com/64410504/145011332-f1c813b7-282a-499f-aae7-f79e766c5f13.png)

### 3. Какой IP адрес у вас в интернете?
83.171.107.190
### 4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой whois
```
vagrant@vagrant:~$ whois -h whois.radb.net 83.171.107.190
route:          83.171.96.0/19
descr:          Rostelecom networks
origin:         AS12389
notify:         ripe@rt.ru
mnt-by:         ROSTELECOM-MNT
created:        2018-10-31T08:06:58Z
last-modified:  2018-10-31T08:06:58Z
source:         RIPE
remarks:        ****************************
remarks:        * THIS OBJECT IS MODIFIED
remarks:        * Please note that all data that is generally regarded as personal
remarks:        * data has been removed from this object.
remarks:        * To view the original object, please query the RIPE Database at:
remarks:        * http://www.ripe.net/whois
remarks:        ****************************
```
Провайдер - Rostelecom

Автономная система AS12389


### 5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой traceroute
Для отображения информации об автономных системах воспульзуемся утилитой traceroute с ключом -A
```
vagrant@vagrant:~$ traceroute -A 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  _gateway (192.168.1.1) [*]  2.830 ms  3.737 ms  4.898 ms
 2  172.18.0.33 (172.18.0.33) [*]  0.726 ms  0.640 ms  0.561 ms
 3  ppp.83-171-104-3.pppoe.avangarddsl.ru (83.171.104.3) [AS12389]  6.442 ms  6.                                                           357 ms  6.281 ms
 4  bbn.nwtelecom.ru (212.48.204.108) [AS12389]  1.147 ms  1.064 ms xe-0-0-0-10g                                                           .M120-2-MURM.nwtelecom.ru (212.48.204.102) [AS12389]  1.916 ms
 5  188.254.2.4 (188.254.2.4) [AS12389]  17.418 ms  17.578 ms  17.500 ms
 6  87.226.194.47 (87.226.194.47) [AS12389]  17.188 ms  17.324 ms  18.634 ms
 7  74.125.244.133 (74.125.244.133) [AS15169]  18.799 ms 74.125.244.181 (74.125.244.181) [AS15169]  18.671 ms 74.125.244.133 (74.125.244.133) [AS15169]  18.585 ms
 8  72.14.232.84 (72.14.232.84) [AS15169]  21.387 ms  20.746 ms  19.344 ms
 9  216.239.48.163 (216.239.48.163) [AS15169]  22.411 ms 142.251.61.219 (142.251.61.219) [AS15169]  22.148 ms 142.250.56.127 (142.250.56.127) [AS15169]  23.247 ms
10  * 142.250.210.47 (142.250.210.47) [AS15169]  25.857 ms 216.239.46.139 (216.239.46.139) [AS15169]  23.058 ms
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  dns.google (8.8.8.8) [AS15169]  22.677 ms  23.592 ms  22.471 ms

```
### 6. Повторите задание 5 в утилите mtr. На каком участке наибольшая задержка - delay?
```
vagrant@vagrant:~$ mtr -z 8.8.8.8
                                                                                             My traceroute  [v0.93]
vagrant (192.168.1.13)                                                                                                                                                                  2021-12-07T11:58:15+0000
Keys:  Help   Display mode   Restart statistics   Order of fields   quit
                                                                                                                                                                        Packets               Pings
 Host                                                                                                                                                                 Loss%   Snt   Last   Avg  Best  Wrst StDev
 1. AS???    _gateway                                                                                                                                                  0.0%    16    2.2   2.9   2.1  11.7   2.3
 2. AS???    172.18.0.33                                                                                                                                               0.0%    15    0.7   0.8   0.6   1.4   0.2
 3. AS12389  ppp.83-171-104-3.pppoe.avangarddsl.ru                                                                                                                     0.0%    15    9.0   4.1   1.1  15.8   4.1
 4. AS12389  bbn.nwtelecom.ru                                                                                                                                          0.0%    15    1.9   2.5   1.2   9.9   2.5
 5. AS12389  188.254.2.6                                                                                                                                               0.0%    15   19.2  19.2  19.0  19.8   0.2
 6. AS12389  87.226.194.47                                                                                                                                             0.0%    15   19.3  19.1  18.9  19.7   0.2
 7. AS15169  74.125.244.133                                                                                                                                            0.0%    15   20.6  28.5  20.5 128.2  27.6
 8. AS15169  142.251.51.187                                                                                                                                            0.0%    15   24.4  34.5  24.0  71.2  17.3
 9. AS15169  142.250.232.179                                                                                                                                           0.0%    15   26.2  25.8  24.9  28.3   0.8
10. (waiting for reply)
11. (waiting for reply)
12. (waiting for reply)
13. (waiting for reply)
14. (waiting for reply)
15. (waiting for reply)
16. AS15169  dns.google                                                                                                                                               64.3%    15   24.2  24.3  24.1  24.4   0.1

```
Наибольшая СРЕДНЯЯ(AVG) задержка на узле 8. AS15169  142.251.51.187 

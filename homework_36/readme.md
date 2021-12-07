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

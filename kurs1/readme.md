1. Создайте виртуальную машину Linux.

Выполнено

2. Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.
```bash
root@vagrant:/home/vagrant# apt install ufw
Reading package lists... Done
Building dependency tree
Reading state information... Done
ufw is already the newest version (0.36-6).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
#ufw уже установлен
#Проверяем статус ufw
root@vagrant:/home/vagrant# ufw status
Status: inactive
# Активируем ufw
root@vagrant:/home/vagrant# ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
root@vagrant:/home/vagrant# ufw status
Status: active
# Разрешаем подключение по 22 порту (ssh):
root@vagrant:/home/vagrant# ufw allow ssh
Rule added
Rule added (v6)
# Разрешаем подключение по 443 порту:
root@vagrant:/home/vagrant# ufw allow 443
Rule added
Rule added (v6)
```
3. Установите hashicorp vault ([инструкция по ссылке](https://learn.hashicorp.com/tutorials/vault/getting-started-install?in=vault/getting-started#install-vault)).

Установлено. Результат проверки командой vault
```
root@vagrant:/home/vagrant# vault
Usage: vault <command> [args]

Common commands:
    read        Read data and retrieves secrets
    write       Write data, configuration, and secrets
    delete      Delete secrets and configuration
    list        List data or secrets
    login       Authenticate locally
    agent       Start a Vault agent
    server      Start a Vault server
    status      Print seal and HA status
    unwrap      Unwrap a wrapped secret

Other commands:
    audit          Interact with audit devices
    auth           Interact with auth methods
    debug          Runs the debug command
    kv             Interact with Vault's Key-Value storage
    lease          Interact with leases
    monitor        Stream log messages from a Vault server
    namespace      Interact with namespaces
    operator       Perform operator-specific tasks
    path-help      Retrieve API help for paths
    plugin         Interact with Vault plugins and catalog
    policy         Interact with policies
    print          Prints runtime configurations
    secrets        Interact with secrets engines
    ssh            Initiate an SSH session
    token          Interact with tokens
```

4. Cоздайте центр сертификации по инструкции ([ссылка](https://learn.hashicorp.com/tutorials/vault/pki-engine?in=vault/secrets-management)) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).

Vault запущен в DEV-режиме.
Центр сертификации создан. В качестве имени использовал имя из инструкции examle.com.
Для использования с nginx сохраняю файл сертификаты и закрытый ключ в файлы при помощи команд:
```bash
root@vagrant:~# json_cert=`vault write -format=json pki_int/issue/example-dot-com common_name="test.example.com" ttl="720h"`    #присваиваем переменной вывод генерации сертификата в формате json
root@vagrant:~# echo $json_cert|jq -r '.data.certificate'>test.example.com.crt      #Сохраняем файл сертификата при помощи утилиты jq
root@vagrant:~# echo $json_cert|jq -r '.data.private_key'>test.example.com.key      #Сохраняем закрытый ключ в файл при помощи утилиты jq
```

5. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.
Сертификат корневого центра установлен в доверенные в хостовой ОС Windows:

![image](https://user-images.githubusercontent.com/64410504/147849641-276b35a6-0f5f-42ce-942d-383912068fd1.png)

6. Установите nginx.

Установлен. Проверка демона:
```
root@vagrant:~# systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2022-01-01 12:15:30 UTC; 1min 5s ago
       Docs: man:nginx(8)
   Main PID: 17484 (nginx)
      Tasks: 3 (limit: 1071)
     Memory: 4.3M
     CGroup: /system.slice/nginx.service
             ├─17484 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
             ├─17485 nginx: worker process
             └─17486 nginx: worker process

Jan 01 12:15:30 vagrant systemd[1]: Starting A high performance web server and a reverse proxy server...
Jan 01 12:15:30 vagrant systemd[1]: Started A high performance web server and a reverse proxy server.
```

7. По инструкции ([ссылка](https://nginx.org/en/docs/http/configuring_https_servers.html)) настройте nginx на https, используя ранее подготовленный сертификат:
  - можно использовать стандартную стартовую страницу nginx для демонстрации работы сервера;
  - можно использовать и другой html файл, сделанный вами;

Для демонстрации использовал стартовую страницу nginx. Настройка:
```bash
root@vagrant:~# mkdir /etc/nginx/ssl                        # Создаем папку для хранения файлов сертификатов и закрытых ключей
root@vagrant:~# cp test.example.com.crt /etc/nginx/ssl      # Копируем файл сертификата
root@vagrant:~# cp test.example.com.key /etc/nginx/ssl      # Копируем файл закрытого ключа
```
Вносим правки в файл конфигурации сайта по умолчанию:
```bash
root@vagrant:~# nano /etc/nginx/sites-enabled/default
```
Расскоменнтируем и добавляем строчки с указанием на файлы сертификата и закрытого ключа:
```
        listen 443 ssl default_server;
        server_name         test.example.com;
        ssl_certificate     /etc/nginx/ssl/test.example.com.crt;
        ssl_certificate_key /etc/nginx/ssl/test.example.com.key;
```
Сохраняем файл. Проверяем коррекность конфига. Перезапускаем nginx для применения изменений:
```bash
root@vagrant:~# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
root@vagrant:~# systemctl restart nginx
```

8. Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.
Для выполнения внес ссылку на test.example.com в файл hosts на хостовой машине. 

Поскольку сертификат сайта выдан подчиненным корневому центру центру сертификации, сертификат промежуточного CA добавил в доверенные.
![image](https://user-images.githubusercontent.com/64410504/148042379-5e4b8937-0e81-4274-89ed-1b34ecaa887c.png)

9. Создайте скрипт, который будет генерировать новый сертификат в vault:
  - генерируем новый сертификат так, чтобы не переписывать конфиг nginx;
  - перезапускаем nginx для применения нового сертификата.

Используя выполняемые ранее команды создаю скрипт следующего содержания:
```bash
json_cert=`vault write -format=json pki_int/issue/example-dot-com common_name="test.example.com" ttl="720h"`
echo $json_cert|jq -r '.data.certificate'>/etc/nginx/ssl/test.example.com.crt
echo $json_cert|jq -r '.data.private_key'>/etc/nginx/ssl/test.example.com.key
systemctl restart nginx
```
Даем права на запуск файла, запускам файл
```bash
root@vagrant:# chmod 755 renew.sh
root@vagrant:# ./renew.sh
```
Для проверки того, что требуемый результат достигнут просматриваем в браузере сертификаты до и после выполнения скрипта. Сравниваем серийные номера сертификатов, убеждаемся, что они отличаются:

До выполнения скрипта:

![image](https://user-images.githubusercontent.com/64410504/148049702-097cd7aa-74c8-49bf-af07-afd4449ae407.png)

После выполнения скрипта:

![image](https://user-images.githubusercontent.com/64410504/148049746-92051619-92f9-4981-9f26-68e4cf7c77a6.png)



10. Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.

Открываем файл crontab при помощи команды:
```bash
root@vagrant:/vagrant# crontab -e

```
Добавляем в него следующие строчки:
```
# выполнять скрипт каждый месяц первого числа в три часа ночи пятнадцать минут #
15 3 1 * * /root/renew.sh
```

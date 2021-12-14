#### 1.Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.
Выполнено
#### 2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
Выполнено:

![image](https://user-images.githubusercontent.com/64410504/145977231-cbf7469e-73d1-4374-98c9-0a689085c844.png)

#### 3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
```
# Установка apache2
vagrant@vagrant:~$ sudo apt-get install apache2
# Генерируем сертификат
vagrant@vagrant:~$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
# Редактируем параметры SSL APACHE
vagrant@vagrant:~$ sudo nano /etc/apache2/conf-available/ssl-params.conf
########################
SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH
SSLProtocol All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
SSLHonorCipherOrder On
# Disable preloading HSTS for now.  You can use the commented out header line that includes
# the "preload" directive if you understand the implications.
# Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
Header always set X-Frame-Options DENY
Header always set X-Content-Type-Options nosniff
# Requires Apache >= 2.4
SSLCompression off
SSLUseStapling on
SSLStaplingCache "shmcb:logs/stapling-cache(150000)"
# Requires Apache >= 2.4.11
SSLSessionTickets Off
########################
# Указываем настройки местоположение файлов сертификата и закрытого ключа
vagrant@vagrant:~$ sudo nano /etc/apache2/sites-available/default-ssl.conf
####
                SSLCertificateFile      /etc/ssl/certs/apache-selfsigned.crt
                SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
####
# Активируем mod_ssl, модуль Apache SSL, и модуль mod_headers
vagrant@vagrant:~$ sudo a2enmod ssl
vagrant@vagrant:~$ sudo a2enmod headers
# Активируем виртуальный хост SSL 
vagrant@vagrant:~$ sudo a2ensite default-ssl
# активируем файл ssl-params.conf для считывания заданных значений:
vagrant@vagrant:~$ sudo a2enconf ssl-params
# Проверяем конфиг апача:
vagrant@vagrant:~$ sudo apache2ctl configtest
Syntax OK
# Перезапускаем apache2
vagrant@vagrant:~$ sudo systemctl restart apache2
```
Открываем в браузере страницук https://localhost/
На предупреждение о некорректном сертификате соглашаемся открыть сайт, видим тестовую страницу apacahe2:
![image](https://user-images.githubusercontent.com/64410504/145985583-9567b820-16c6-4d11-af0e-08c8a7d133ac.png)

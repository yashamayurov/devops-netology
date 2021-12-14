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

#### 4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).
Для теста использована утилита testssl.sh
```
vagrant@vagrant:~/testssl.sh$ git clone --depth 1 https://github.com/drwetter/testssl.sh.git
vagrant@vagrant:~ cd testssl.sh
# Для тестирования использовал платформу вебинаров своей организации BigBlueButton 
vagrant@vagrant:~/testssl.sh$ ./testssl.sh https://vks.mood51.ru

###########################################################
    testssl.sh       3.1dev from https://testssl.sh/dev/
    (2201a28 2021-12-13 18:24:34 -- )

      This program is free software. Distribution and
             modification under GPLv2 permitted.
      USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

       Please file bugs @ https://testssl.sh/bugs/

###########################################################

 Using "OpenSSL 1.0.2-chacha (1.0.2k-dev)" [~183 ciphers]
 on vagrant:./bin/openssl.Linux.x86_64
 (built: "Jan 18 17:12:17 2019", platform: "linux-x86_64")


 Start 2021-12-14 11:13:32        -->> 95.54.192.106:443 (vks.mood51.ru) <<--

 rDNS (95.54.192.106):   95-54-192-106.static.murmansk.dslavangard.ru.
 Service detected:       HTTP


 Testing protocols via sockets except NPN+ALPN

 SSLv2      not offered (OK)
 SSLv3      not offered (OK)
 TLS 1      offered (deprecated)
 TLS 1.1    offered (deprecated)
 TLS 1.2    offered (OK)
 TLS 1.3    not offered and downgraded to a weaker protocol
 NPN/SPDY   http/1.1 (advertised)
 ALPN/HTTP2 http/1.1 (offered)

 Testing cipher categories

 NULL ciphers (no encryption)                      not offered (OK)
 Anonymous NULL Ciphers (no authentication)        not offered (OK)
 Export ciphers (w/o ADH+NULL)                     not offered (OK)
 LOW: 64 Bit + DES, RC[2,4], MD5 (w/o export)      not offered (OK)
 Triple DES Ciphers / IDEA                         offered
 Obsoleted CBC ciphers (AES, ARIA etc.)            offered
 Strong encryption (AEAD ciphers) with no FS       offered (OK)
 Forward Secrecy strong encryption (AEAD ciphers)  offered (OK)


 Testing server's cipher preferences

 Has server cipher order?     yes (OK)
 Negotiated protocol          TLSv1.2
 Negotiated cipher            ECDHE-RSA-AES128-GCM-SHA256, 256 bit ECDH (P-256)
 Cipher per protocol

Hexcode  Cipher Suite Name (OpenSSL)       KeyExch.   Encryption  Bits     Cipher Suite Name (IANA/RFC)
-----------------------------------------------------------------------------------------------------------------------------
SSLv2
 -
SSLv3
 -
TLSv1 (server order)
 xc013   ECDHE-RSA-AES128-SHA              ECDH 256   AES         128      TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
 x33     DHE-RSA-AES128-SHA                DH 4096    AES         128      TLS_DHE_RSA_WITH_AES_128_CBC_SHA
 xc012   ECDHE-RSA-DES-CBC3-SHA            ECDH 256   3DES        168      TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA
 x16     EDH-RSA-DES-CBC3-SHA              DH 4096    3DES        168      TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
 x2f     AES128-SHA                        RSA        AES         128      TLS_RSA_WITH_AES_128_CBC_SHA
 x0a     DES-CBC3-SHA                      RSA        3DES        168      TLS_RSA_WITH_3DES_EDE_CBC_SHA
TLSv1.1 (server order)
 xc013   ECDHE-RSA-AES128-SHA              ECDH 256   AES         128      TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
 x33     DHE-RSA-AES128-SHA                DH 4096    AES         128      TLS_DHE_RSA_WITH_AES_128_CBC_SHA
 xc012   ECDHE-RSA-DES-CBC3-SHA            ECDH 256   3DES        168      TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA
 x16     EDH-RSA-DES-CBC3-SHA              DH 4096    3DES        168      TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
 x2f     AES128-SHA                        RSA        AES         128      TLS_RSA_WITH_AES_128_CBC_SHA
 x0a     DES-CBC3-SHA                      RSA        3DES        168      TLS_RSA_WITH_3DES_EDE_CBC_SHA
TLSv1.2 (server order)
 xc02f   ECDHE-RSA-AES128-GCM-SHA256       ECDH 256   AESGCM      128      TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
 x9e     DHE-RSA-AES128-GCM-SHA256         DH 4096    AESGCM      128      TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
 xc027   ECDHE-RSA-AES128-SHA256           ECDH 256   AES         128      TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
 xc013   ECDHE-RSA-AES128-SHA              ECDH 256   AES         128      TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
 x67     DHE-RSA-AES128-SHA256             DH 4096    AES         128      TLS_DHE_RSA_WITH_AES_128_CBC_SHA256
 x33     DHE-RSA-AES128-SHA                DH 4096    AES         128      TLS_DHE_RSA_WITH_AES_128_CBC_SHA
 xc012   ECDHE-RSA-DES-CBC3-SHA            ECDH 256   3DES        168      TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA
 x16     EDH-RSA-DES-CBC3-SHA              DH 4096    3DES        168      TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
 x9c     AES128-GCM-SHA256                 RSA        AESGCM      128      TLS_RSA_WITH_AES_128_GCM_SHA256
 x3c     AES128-SHA256                     RSA        AES         128      TLS_RSA_WITH_AES_128_CBC_SHA256
 x2f     AES128-SHA                        RSA        AES         128      TLS_RSA_WITH_AES_128_CBC_SHA
 x0a     DES-CBC3-SHA                      RSA        3DES        168      TLS_RSA_WITH_3DES_EDE_CBC_SHA
TLSv1.3
 -


 Testing robust forward secrecy (FS) -- omitting Null Authentication/Encryption, 3DES, RC4

 FS is offered (OK)           ECDHE-RSA-AES128-GCM-SHA256 ECDHE-RSA-AES128-SHA256 ECDHE-RSA-AES128-SHA DHE-RSA-AES128-GCM-SHA256 DHE-RSA-AES128-SHA256 DHE-RSA-AES128-SHA
 Elliptic curves offered:     prime256v1
 DH group offered:            Unknown DH group (4096 bits)

 Testing server defaults (Server Hello)

 TLS extensions (standard)    "renegotiation info/#65281" "EC point formats/#11" "session ticket/#35" "heartbeat/#15" "next protocol/#13172" "application layer protocol negotiation/#16"
 Session Ticket RFC 5077 hint 600 seconds, session tickets keys seems to be rotated < daily
 SSL Session ID support       yes
 Session Resumption           Tickets: yes, ID: yes
 TLS clock skew               Random values, no fingerprinting possible
 Client Authentication        none
 Signature Algorithm          SHA256 with RSA
 Server key size              RSA 2048 bits (exponent is 65537)
 Server key usage             Digital Signature, Key Encipherment
 Server extended key usage    TLS Web Server Authentication, TLS Web Client Authentication
 Serial                       03C514EADDE61855CB2A6B881EF15F9AD643 (OK: length 18)
 Fingerprints                 SHA1 B4553469984E6AA59FBB33E47DDBFD79EB2E6E99
                              SHA256 AF6526DB9B5A29A991C6B423E82A383315C7865BD3E68645B7F497CDB71F18AA
 Common Name (CN)             vks.mood51.ru
 subjectAltName (SAN)         vks.mood51.ru
 Trust (hostname)             Ok via SAN and CN (same w/o SNI)
 Chain of trust               Ok
 EV cert (experimental)       no
 Certificate Validity (UTC)   55 >= 30 days (2021-11-10 03:40 --> 2022-02-08 03:40)
 ETS/"eTLS", visibility info  not present
 Certificate Revocation List  --
 OCSP URI                     http://r3.o.lencr.org
 OCSP stapling                not offered
 OCSP must staple extension   --
 DNS CAA RR (experimental)    not offered
 Certificate Transparency     yes (certificate extension)
 Certificates provided        3
 Issuer                       R3 (Let's Encrypt from US)
 Intermediate cert validity   #1: ok > 40 days (2025-09-15 16:00). R3 <-- ISRG Root X1
                              #2: ok > 40 days (2024-09-30 18:14). ISRG Root X1 <-- DST Root CA X3
 Intermediate Bad OCSP (exp.) Ok


 Testing HTTP header response @ "/"

 HTTP Status Code             200 OK
 HTTP clock skew              0 sec from localtime
 Strict Transport Security    not offered
 Public Key Pinning           --
 Server banner                nginx/1.10.3 (Ubuntu)
 Application banner           --
 Cookie(s)                    (none issued at "/")
 Security headers             Cache-Control: max-age=60
 Reverse Proxy banner         --


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), timed out
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK)
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           supported (OK)
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    potentially NOT ok, "gzip" HTTP compression detected. - only supplied "/" tested
                                           Can be ignored for static pages or if no secrets in the page
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK), no SSLv3 support
 TLS_FALLBACK_SCSV (RFC 7507)              Downgrade attack prevention supported (OK)
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    VULNERABLE, uses 64 bit block ciphers
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services
                                           https://censys.io/ipv4?q=AF6526DB9B5A29A991C6B423E82A383315C7865BD3E68645B7F497CDB71F18AA could help you to find out
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no common prime detected
 BEAST (CVE-2011-3389)                     TLS1: ECDHE-RSA-AES128-SHA DHE-RSA-AES128-SHA ECDHE-RSA-DES-CBC3-SHA EDH-RSA-DES-CBC3-SHA AES128-SHA DES-CBC3-SHA
                                           VULNERABLE -- but also supports higher protocols  TLSv1.1 TLSv1.2 (likely mitigated)
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK) - CAMELLIA or ECDHE_RSA GCM ciphers found
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Running client simulations (HTTP) via sockets

 Browser                      Protocol  Cipher Suite Name (OpenSSL)       Forward Secrecy
------------------------------------------------------------------------------------------------
 Android 4.4.2                TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Android 5.0.0                TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Android 6.0                  TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Android 7.0 (native)         TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Android 8.1 (native)         TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Android 9.0 (native)         TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Android 10.0 (native)        TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Chrome 74 (Win 10)           TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Chrome 79 (Win 10)           TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Firefox 66 (Win 8.1/10)      TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Firefox 71 (Win 10)          TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 IE 6 XP                      No connection
 IE 8 Win 7                   TLSv1.0   ECDHE-RSA-AES128-SHA              256 bit ECDH (P-256)
 IE 8 XP                      TLSv1.0   DES-CBC3-SHA                      No FS
 IE 11 Win 7                  TLSv1.2   DHE-RSA-AES128-GCM-SHA256         4096 bit DH
 IE 11 Win 8.1                TLSv1.2   DHE-RSA-AES128-GCM-SHA256         4096 bit DH
 IE 11 Win Phone 8.1          TLSv1.2   ECDHE-RSA-AES128-SHA256           256 bit ECDH (P-256)
 IE 11 Win 10                 TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Edge 15 Win 10               TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Edge 17 (Win 10)             TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Opera 66 (Win 10)            TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Safari 9 iOS 9               TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Safari 9 OS X 10.11          TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Safari 10 OS X 10.12         TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Safari 12.1 (iOS 12.2)       TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Safari 13.0 (macOS 10.14.6)  TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Apple ATS 9 iOS 9            TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Java 6u45                    No connection
 Java 7u25                    TLSv1.0   ECDHE-RSA-AES128-SHA              256 bit ECDH (P-256)
 Java 8u161                   TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Java 11.0.2 (OpenJDK)        TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Java 12.0.1 (OpenJDK)        TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 OpenSSL 1.0.2e               TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 OpenSSL 1.1.0l (Debian)      TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 OpenSSL 1.1.1d (Debian)      TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)
 Thunderbird (68.3)           TLSv1.2   ECDHE-RSA-AES128-GCM-SHA256       256 bit ECDH (P-256)


 Rating (experimental)

 Rating specs (not complete)  SSL Labs's 'SSL Server Rating Guide' (version 2009q from 2020-01-30)
 Specification documentation  https://github.com/ssllabs/research/wiki/SSL-Server-Rating-Guide
 Protocol Support (weighted)  95 (28)
 Key Exchange     (weighted)  90 (27)
 Cipher Strength  (weighted)  80 (32)
 Final Score                  87
 Overall Grade                B
 Grade cap reasons            Grade capped to B. TLS 1.1 offered
                              Grade capped to B. TLS 1.0 offered
                              Grade capped to A. HSTS is not offered

 Done 2021-12-14 11:14:46 [  75s] -->> 95.54.192.106:443 (vks.mood51.ru) <<--
```
#### 5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.
```
# Установка SSH-сервера
vagrant@vagrant:~$ sudo apt-get install openssh-server
# Генерация ключей 
vagrant@vagrant:~$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/vagrant/.ssh/id_rsa
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:9JvnA8LVCRSBPwNlzNaTlm7V4xMIfvccawkmHQ7rf2k vagrant@vagrant
The key's randomart image is:
+---[RSA 3072]----+
|         ***.+.. |
|        o.* @.oo.|
|        .+ O X.oo|
|       . .* O o+=|
|       .S..=   ++|
|        o .o. . .|
|         .o... E |
|           o. o  |
|            ..   |
+----[SHA256]-----+

# Копируем открытый ключ на другой сервер:
vagrant@vagrant:~$ ssh-copy-id vagrant@192.168.1.32
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/vagrant/.ssh/id_rsa.pub"
The authenticity of host '192.168.1.32 (192.168.1.32)' can't be established.
ECDSA key fingerprint is SHA256:wSHl+h4vAtTT7mbkj2lbGyxWXWTUf6VUliwpncjwLPM.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@192.168.1.32's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@192.168.1.32'"
and check to make sure that only the key(s) you wanted were added.

#Подключаемся к другому серверу:
vagrant@vagrant:~$ ssh vagrant@192.168.1.32
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue 14 Dec 2021 06:34:52 PM UTC

  System load:  0.1               Processes:             113
  Usage of /:   2.5% of 61.31GB   Users logged in:       1
  Memory usage: 21%               IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 192.168.1.32


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Tue Dec 14 18:30:30 2021 from 192.168.1.33
```
#### 6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.
```
# Переименовываем файл закрытого ключа:
vagrant@vagrant:~$ cd /home/vagrant/.ssh/
vagrant@vagrant:~/.ssh$ mv id_rsa id_rsa_test

# Создаем конфиг SSH 
vagrant@vagrant:~/.ssh$ touch ~/.ssh/config && chmod 600 ~/.ssh/config

# Вносим правки в конфиг:
vagrant@vagrant:~/.ssh$ nano config
##################################
Host server2
 HostName 192.168.1.32
 IdentityFile ~/.ssh/id_rsa_test
 User vagrant
##################################

# Покдлючаемся по имени сервера:
vagrant@vagrant:~/.ssh$ ssh server2
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue 14 Dec 2021 06:55:41 PM UTC

  System load:  0.08              Processes:             112
  Usage of /:   2.5% of 61.31GB   Users logged in:       1
  Memory usage: 21%               IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 192.168.1.32


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Tue Dec 14 18:51:40 2021 from 192.168.1.33

```
```
#### 7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.
# Запускаем tcpdump
root@vagrant:/home/vagrant# tcpdump -w 0001.pcap -c 100 -i eth1
```

Сриншот WireShark, в котором открыт файл:
![image](https://user-images.githubusercontent.com/64410504/146063240-4d385868-fa4c-4a84-b053-7615dc26728b.png)

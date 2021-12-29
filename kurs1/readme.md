1. Создайте виртуальную машину Linux.
Выполнено
3. Установите ufw и разрешите к этой машине сессии на порты 22 и 443, при этом трафик на интерфейсе localhost (lo) должен ходить свободно на все порты.
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
1. Установите hashicorp vault ([инструкция по ссылке](https://learn.hashicorp.com/tutorials/vault/getting-started-install?in=vault/getting-started#install-vault)).
5. Cоздайте центр сертификации по инструкции ([ссылка](https://learn.hashicorp.com/tutorials/vault/pki-engine?in=vault/secrets-management)) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).
6. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.
7. Установите nginx.
8. По инструкции ([ссылка](https://nginx.org/en/docs/http/configuring_https_servers.html)) настройте nginx на https, используя ранее подготовленный сертификат:
  - можно использовать стандартную стартовую страницу nginx для демонстрации работы сервера;
  - можно использовать и другой html файл, сделанный вами;
9. Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.
10. Создайте скрипт, который будет генерировать новый сертификат в vault:
  - генерируем новый сертификат так, чтобы не переписывать конфиг nginx;
  - перезапускаем nginx для применения нового сертификата.
11. Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.


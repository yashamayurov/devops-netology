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
5. Cоздайте центр сертификации по инструкции ([ссылка](https://learn.hashicorp.com/tutorials/vault/pki-engine?in=vault/secrets-management)) и выпустите сертификат для использования его в настройке веб-сервера nginx (срок жизни сертификата - месяц).

Центр сертификации создан. В качестве имени использовал имя из инструкции examle.com.

7. Установите корневой сертификат созданного центра сертификации в доверенные в хостовой системе.
Сертификат корневого центра установлен в доверенные в хостовой ОС Windows:

![image](https://user-images.githubusercontent.com/64410504/147849641-276b35a6-0f5f-42ce-942d-383912068fd1.png)

9. Установите nginx.
10. По инструкции ([ссылка](https://nginx.org/en/docs/http/configuring_https_servers.html)) настройте nginx на https, используя ранее подготовленный сертификат:
  - можно использовать стандартную стартовую страницу nginx для демонстрации работы сервера;
  - можно использовать и другой html файл, сделанный вами;
11. Откройте в браузере на хосте https адрес страницы, которую обслуживает сервер nginx.
12. Создайте скрипт, который будет генерировать новый сертификат в vault:
  - генерируем новый сертификат так, чтобы не переписывать конфиг nginx;
  - перезапускаем nginx для применения нового сертификата.
13. Поместите скрипт в crontab, чтобы сертификат обновлялся какого-то числа каждого месяца в удобное для вас время.


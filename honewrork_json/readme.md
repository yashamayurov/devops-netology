### Как сдавать задания

Вы уже изучили блок «Системы управления версиями», и начиная с этого занятия все ваши работы будут приниматься ссылками на .md-файлы, размещённые в вашем публичном репозитории.

Скопируйте в свой .md-файл содержимое этого файла; исходники можно посмотреть [здесь](https://raw.githubusercontent.com/netology-code/sysadm-homeworks/devsys10/04-script-03-yaml/README.md). Заполните недостающие части документа решением задач (заменяйте `???`, ОСТАЛЬНОЕ В ШАБЛОНЕ НЕ ТРОГАЙТЕ чтобы не сломать форматирование текста, подсветку синтаксиса и прочее, иначе можно отправиться на доработку) и отправляйте на проверку. Вместо логов можно вставить скриншоты по желани.

# Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"


## Обязательная задача 1
Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : "7175"
            }
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }
```
  Нужно найти и исправить все ошибки, которые допускает наш сервис

## Обязательная задача 2
В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: `{ "имя сервиса" : "его IP"}`. Формат записи YAML по одному сервису: `- имя сервиса: его IP`. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

### Ваш скрипт:
```python
import socket   # Модуль для работы с сетью
import json     # Модуль для работы с json
import yaml     # Модуль для работы с yaml
import copy     # Модуль, необходимы для полного копирования словаря



hostlist1 = dict.fromkeys(["drive.google.com", "mail.google.com", "google.com"])  # Объявляем словарь со списком DNS-имен, в качестве ключей
hostlist2 = {}
serviceList = []                # Список сервисов



while True:                                         # Бесконечный цикл                                 
    
    for dnsName in hostlist1:                          # Перебор элементов словаря           
        ip = socket.gethostbyname(dnsName)             # Разрешаем DNS имя
        hostlist1[dnsName] = ip                        # Присваиваем элементу словаря значение IP-адреса
        dns_ip = {dnsName:ip}                       # Формируем словарь словарь из одной пары DNSname: ip-address
        serviceList.append(dns_ip)                  # Добавляем в список словарь
        if hostlist2:                                  # Проверям присваивались ли значения в словарь hostlist2 
                                                       #- при первом проходе цикла словари hostlist1 и hostlist2 различаются и это не ошибка
            if hostlist1[dnsName]!=hostlist2[dnsName]: # Если значения IP-разные выводим сообщение об ошибке
                print(f'Erorr!!! {dnsName} changed from {hostlist2[dnsName]} to {hostlist1[dnsName]}')
            else:                                      # Если значения одинаковые -  
                print(f'{dnsName} is {ip}')            # Выводим пару DNS-имя - IP-адрес
    hostlist2 = copy.deepcopy(hostlist1)               # Копируем словарь для сравнения при в следующей итерации цикла
    service = dict(services=serviceList)               # Создаем словарь с видом "services: Список словарей"

    with open('F:\\nettology\\Python\\services.json', 'w') as jsonfile:  # Записываем json
        json.dump(service, jsonfile)
    with open('F:\\nettology\\Python\\services.yaml', 'w') as yamlfile:     # Записываем yaml
        yaml.dump(service, yamlfile)
    serviceList = []

```

### Вывод скрипта при запуске при тестировании:
```
C:/Users/iasha/AppData/Local/Programs/Python/Python310/python.exe c:/Users/iasha/homework/python/4.py    
drive.google.com is 10.10.10.13
mail.google.com is 64.233.165.18
google.com is 64.233.165.138    
drive.google.com is 10.10.10.13
mail.google.com is 64.233.165.18
google.com is 64.233.165.138
Erorr!!! drive.google.com changed from 10.10.10.13 to 10.10.10.14
mail.google.com is 64.233.165.18
google.com is 64.233.165.138
drive.google.com is 10.10.10.14
mail.google.com is 64.233.165.18
google.com is 64.233.165.138
Traceback (most recent call last):
  File "c:\Users\iasha\homework\python\4.py", line 8, in <module>
    time.sleep(3)
KeyboardInterrupt
```

### json-файл(ы), который(е) записал ваш скрипт:
```json
{"services": [{"drive.google.com": "173.194.222.194"}, {"mail.google.com": "64.233.161.18"}, {"google.com": "173.194.222.101"}]}
```

### yml-файл(ы), который(е) записал ваш скрипт:
```yaml
services:
- drive.google.com: 173.194.222.194
- mail.google.com: 64.233.161.18
- google.com: 173.194.222.101

```

## Дополнительное задание (со звездочкой*) - необязательно к выполнению

Так как команды в нашей компании никак не могут прийти к единому мнению о том, какой формат разметки данных использовать: JSON или YAML, нам нужно реализовать парсер из одного формата в другой. Он должен уметь:
   * Принимать на вход имя файла
   * Проверять формат исходного файла. Если файл не json или yml - скрипт должен остановить свою работу
   * Распознавать какой формат данных в файле. Считается, что файлы *.json и *.yml могут быть перепутаны
   * Перекодировать данные из исходного формата во второй доступный (из JSON в YAML, из YAML в JSON)
   * При обнаружении ошибки в исходном файле - указать в стандартном выводе строку с ошибкой синтаксиса и её номер
   * Полученный файл должен иметь имя исходного файла, разница в наименовании обеспечивается разницей расширения файлов

### Ваш скрипт:
```python
???
```

### Пример работы скрипта:
???

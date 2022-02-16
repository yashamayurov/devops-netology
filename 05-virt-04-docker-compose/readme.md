## Задача 1

Создать собственный образ операционной системы с помощью Packer.

Для получения зачета, вам необходимо предоставить:
- Скриншот страницы, как на слайде из презентации (слайд 37).

#### Решение:

Для решения задания воспользовался инструкцией на Яндекс.Облаке.
1. Скачал утилиту packer с официального сайта
2. Установил утилиту yc для работы с Яндекс.Облако. Проивел ее инициализацию yc init
3. Создал папку netology в облаке и сеть с подсетью
4. Подготовил файл image.json следующего содержания:
```json
{
  "builders": [
    {
      "type":      "yandex",
      "token":     "AQAEA7qgtYQrAATuwYto7rijDENcg3gH-jk5RWw",
      "folder_id": "b1g41gv87uog0ndnkokc",
      "zone":      "ru-central1-c",

      "image_name":        "debian-9-nginx-{{isotime | clean_resource_name}}",
      "image_family":      "debian-web-server",
      "image_description": "netology debian with nginx",

      "source_image_family": "debian-9",
      "subnet_id":           "b0c23a6fmc7dhnulbofd",
      "use_ipv4_nat":        true,
      "disk_type":           "network-ssd",
      "ssh_username":        "debian"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "inline": [
        "echo 'updating APT'",
        "sudo apt-get update -y",
        "sudo apt-get install -y nginx",
        "sudo su -",
        "sudo systemctl enable nginx.service",
        "curl localhost"
      ]
    }
  ]
}
```
5. Запуск сборки образа:
```
F:\packer> ./packer build image.json

.....

Build 'yandex' finished after 1 minute 53 seconds.

==> Wait completed after 1 minute 53 seconds

==> Builds finished. The artifacts of successful builds are:
--> yandex: A disk image was created: debian-9-nginx-2022-02-16t10-22-26z (id: fd82crq5lt6jdq6r186p) with family name debian-web-server
```
6.  Вывод списка образов:
```
PS F:\packer> yc compute image list
+----------------------+-------------------------------------+-------------------+----------------------+--------+
|          ID          |                NAME                 |      FAMILY       |     PRODUCT IDS      | STATUS |
+----------------------+-------------------------------------+-------------------+----------------------+--------+
| fd82crq5lt6jdq6r186p | debian-9-nginx-2022-02-16t10-22-26z | debian-web-server | f2ei8jnhdc11nm8cmevq | READY  |
+----------------------+-------------------------------------+-------------------+----------------------+--------+
```

## Задача 2

Создать вашу первую виртуальную машину в Яндекс.Облаке.

Для получения зачета, вам необходимо предоставить:
- Скриншот страницы свойств созданной ВМ, как на примере ниже:

<p align="center">
  <img width="1200" height="600" src="./assets/yc_01.png">
</p>

## Задача 3

Создать ваш первый готовый к боевой эксплуатации компонент мониторинга, состоящий из стека микросервисов.

Для получения зачета, вам необходимо предоставить:
- Скриншот работающего веб-интерфейса Grafana с текущими метриками, как на примере ниже
<p align="center">
  <img width="1200" height="600" src="./assets/yc_02.png">
</p>

## Задача 4 (*)

Создать вторую ВМ и подключить её к мониторингу развёрнутому на первом сервере.

Для получения зачета, вам необходимо предоставить:
- Скриншот из Grafana, на котором будут отображаться метрики добавленного вами сервера.


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

#### Решение:
Выполняем команду:
```
PS F:\packer> yc compute instance create --name debian-9-nginx-2022-02-16t10-22-26z --zone ru-central1-c --create-boot-disk name=disk1,size=50,image-id=fd82crq5lt6jdq6r186p
done (36s)
id: ef3dluigi1ajjc346955
folder_id: b1g41gv87uog0ndnkokc
created_at: "2022-02-16T10:58:05Z"
name: debian-9-nginx-2022-02-16t10-22-26z
zone_id: ru-central1-c
platform_id: standard-v2
resources:
  memory: "2147483648"
  cores: "2"
  core_fraction: "100"
status: RUNNING
boot_disk:
  mode: READ_WRITE
  device_name: ef3bj58ea3amnpu1uv11
  auto_delete: true
  disk_id: ef3bj58ea3amnpu1uv11
network_interfaces:
- index: "0"
  mac_address: d0:0d:da:fa:50:90
  subnet_id: b0c23a6fmc7dhnulbofd
  primary_v4_address:
    address: 10.130.0.3
fqdn: ef3dluigi1ajjc346955.auto.internal
scheduling_policy: {}
network_settings:
  type: STANDARD
placement_policy: {}
```
Скриншот с интерфейса Яндекс.Облако:
![image](https://user-images.githubusercontent.com/64410504/154251665-23271de6-7033-4b9a-b4cd-3e407ea3fc77.png)


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


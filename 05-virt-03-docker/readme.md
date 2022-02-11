## Задача 1

Сценарий выполения задачи:

- создайте свой репозиторий на https://hub.docker.com;
```
Выполнено
```
- выберете любой образ, который содержит веб-сервер Nginx;
- создайте свой fork образа;
- реализуйте функциональность:
запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.

#### Решение:
Для решения использовал виртуальную машину Ubuntu.
Создаем директорию /opt/docker/mynginx, в которой создаем файл index.html с кодом, приведенным в заданиии и Dockerfile следущего содержания:
```
FROM nginx
LABEL maintainer="yakov@mayurov.ru"
COPY index.html /usr/share/nginx/html/index.html
```
Собираем образ Docker:
```
docker build -t yashamayurov/mynginx:v0.0.1 .
```
Запускам образ с пробросом 80 порта:
```
root@vagrant:/opt/docker/mynginx# docker run -p 80:80 yashamayurov/mynginx:v0.0.1
```
Проверяем ответа nginx
```
vagrant@vagrant:~$ curl localhost
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Авторизуемся на Dokerhub, отправляем образ в репозиторий:
```
root@vagrant:/opt/docker/mynginx# docker login --username yashamayurov
root@vagrant:/opt/docker/mynginx# docker push yashamayurov/mynginx:v0.0.1
```
Ссылка на репозиторий:

https://hub.docker.com/r/yashamayurov/mynginx


## Задача 2

Посмотрите на сценарий ниже и ответьте на вопрос:
"Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

- Высоконагруженное монолитное java веб-приложение;
```
Для данного вариант имеет смысл использовать физическую машину для быстрого доступа к аппаратным ресурсам.
```
- Nodejs веб-приложение;
- Мобильное приложение c версиями для Android и iOS;
```
Имеет смысл использовать Docker из за разнобразия платформ запуска приложений
```
- Шина данных на базе Apache Kafka;
```
Поскольку продукт может разворачиваться на неопределенном количестве серверов имеет смысл использоваться docker
```
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
- Мониторинг-стек на базе Prometheus и Grafana;
- MongoDB, как основное хранилище данных для java-приложения;
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.

## Задача 3

- Запустите первый контейнер из образа ***centos*** c любым тэгом в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
```
root@vagrant:/home/vagrant# mkdir data
root@vagrant:/home/vagrant# docker pull centos
root@vagrant:/home/vagrant# docker run -i -d -v $PWD/data:/data centos /bin/bash
```
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;

```
root@vagrant:/home/vagrant# docker pull debian
root@vagrant:/home/vagrant# docker run -i -d -v $PWD/data:/data debian /bin/bash
```
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```;
```
root@vagrant:/home/vagrant# docker exec -i 138d470ad769 /bin/bash
cd /data
echo text>test.txt
exit 
```
- Добавьте еще один файл в папку ```/data``` на хостовой машине;
```
root@vagrant:/home/vagrant# cd data
root@vagrant:/home/vagrant/data# echo test2>>text2.txt
```
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.
```
root@vagrant:/home/vagrant/data# docker exec -i bc655e49bba1 /bin/bash
cd /data
ls
text.txt
text2.txt
cat text.txt
test
cat text2.txt
test2
```
## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

Соберите Docker образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.


---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

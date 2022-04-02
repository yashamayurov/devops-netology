## Задача 1

В этом задании вы потренируетесь в:
- установке elasticsearch
- первоначальном конфигурировании elastcisearch
- запуске elasticsearch в docker

Используя докер образ [centos:7](https://hub.docker.com/_/centos) как базовый и 
[документацию по установке и запуску Elastcisearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html):

- составьте Dockerfile-манифест для elasticsearch
- соберите docker-образ и сделайте `push` в ваш docker.io репозиторий
- запустите контейнер из получившегося образа и выполните запрос пути `/` c хост-машины

Требования к `elasticsearch.yml`:
- данные `path` должны сохраняться в `/var/lib`
```
path.data: /var/lib
```
- имя ноды должно быть `netology_test`
```
node.name: netology_test
```

В ответе приведите:
- текст Dockerfile манифеста
```dockerfile
FROM centos:7
LABEL maintainer="yakov@mayurov.ru"
# По причине недоступности файлов elasticsearch с роосийских IP 
# установил nginx на вирутальной машине и разместил файлы
ARG HTTP_URL=http://192.168.1.18/
ARG TARGZ=elasticsearch-8.1.1-linux-x86_64.tar.gz
ARG SHA=elasticsearch-8.1.1-linux-x86_64.tar.gz.sha512

RUN adduser elastuser
RUN yum install wget -y \
    && yum install perl-Digest-SHA -y \
    && wget ${HTTP_URL}${TARGZ} ${HTTP_URL}${SHA} \
    && shasum -a 512 -c ${SHA} \
    && tar -xzf ${TARGZ} \
    && rm ${TARGZ} ${SHA} \
    && cd /elasticsearch-8.1.1/ \
    && chmod -R 757 /elasticsearch-8.1.1 \
    && chmod -R 757 /var/lib     
RUN echo "vm.max_map_count=262144" >> /etc/sysctl.conf
WORKDIR /elasticsearch-8.1.1
EXPOSE 9200
COPY elasticsearch.yml /elasticsearch-8.1.1/config
USER elastuser
CMD ./bin/elasticsearch
```
- ссылку на образ в репозитории dockerhub
https://hub.docker.com/r/yashamayurov/myelast
- ответ `elasticsearch` на запрос пути `/` в json виде
```json
{
  "name" : "netology_test",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "8S6mhIBOTyC3hpvhXTYYMg",
  "version" : {
    "number" : "8.1.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "d0925dd6f22e07b935750420a3155db6e5c58381",
    "build_date" : "2022-03-17T22:01:32.658689558Z",
    "build_snapshot" : false,
    "lucene_version" : "9.0.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```


Далее мы будем работать с данным экземпляром elasticsearch.

## Задача 2

В этом задании вы научитесь:
- создавать и удалять индексы
- изучать состояние кластера
- обосновывать причину деградации доступности данных

Ознакомтесь с [документацией](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-create-index.html) 
и добавьте в `elasticsearch` 3 индекса, в соответствии со таблицей:

| Имя | Количество реплик | Количество шард |
|-----|-------------------|-----------------|
| ind-1| 0 | 1 |
| ind-2 | 1 | 2 |
| ind-3 | 2 | 4 |

Получите список индексов и их статусов, используя API и **приведите в ответе** на задание.
```
root@vagrant:~# curl 'localhost:9200/_cat/indices?v&pretty'
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   ind-1 X16-_VYtTB2QK2PTDRm-9A   1   0          0            0       225b           225b
yellow open   ind-3 FBQUKUOqQciwJGdGoeMYCA   4   2          0            0       900b           900b
yellow open   ind-2 oIxSgiuCSGaw2Ekx14KA7w   2   1          0            0       450b           450b
```

Получите состояние кластера `elasticsearch`, используя API.
```
root@vagrant:~# curl localhost:9200/_cat/health
1648809645 10:40:45 elasticsearch yellow 1 1 8 8 0 0 10 0 - 44.4%
```
Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?
```
Полагаю, что причина в том, что в описании индексов имеются реплики, но elasticsearch запущен в одиночном режиме
```
Удалите все индексы.
```bash
root@vagrant:~# curl -X DELETE "localhost:9200/ind-3?pretty"
{
  "acknowledged" : true
}
root@vagrant:~# curl -X DELETE "localhost:9200/ind-2?pretty"
{
  "acknowledged" : true
}
root@vagrant:~# curl -X DELETE "localhost:9200/ind-1?pretty"
{
  "acknowledged" : true
}
```
**Важно**

При проектировании кластера elasticsearch нужно корректно рассчитывать количество реплик и шард,
иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

## Задача 3

В данном задании вы научитесь:
- создавать бэкапы данных
- восстанавливать индексы из бэкапов

Создайте директорию `{путь до корневой директории с elasticsearch в образе}/snapshots`.

Используя API [зарегистрируйте](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-register-repository.html#snapshots-register-repository) 
данную директорию как `snapshot repository` c именем `netology_backup`.

**Приведите в ответе** запрос API и результат вызова API для создания репозитория.
```bash
root@vagrant:~# curl -X PUT "localhost:9200/_snapshot/netology_backup?pretty" -H 'Content-Type: application/json' -d'
> {
>   "type": "fs",
>   "settings": {
>     "location": "/elasticsearch-8.1.1/snapshots"
>   }
> }
> '
{
  "acknowledged" : true
}
```
Создайте индекс `test` с 0 реплик и 1 шардом и **приведите в ответе** список индексов.
```bash
root@vagrant:~# curl 'localhost:9200/_cat/indices?v&pretty'
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test  jALvbqmXRjubnOeJGU6Yow   1   0          0            0       225b           225b
```
[Создайте `snapshot`](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-take-snapshot.html) 
состояния кластера `elasticsearch`.
```bash
curl -X PUT "http://192.168.1.18:9200/_snapshot/netology_backup/%3Cmy_snapshot_%7Bnow%2Fd%7D%3E?pretty"'
HTTP/1.1 200 OK
X-elastic-product: Elasticsearch
content-type: application/json
content-encoding: gzip
content-length: 50

{
  "accepted": true
}
```
**Приведите в ответе** список файлов в директории со `snapshot`ами.
```bash
root@vagrant:~# docker exec -i -t bc507330a916 ls /elasticsearch-8.1.1/snapshots
index-0       indices                          snap-8Y_offk8SZ2atSjqW2r2TA.dat
index.latest  meta-8Y_offk8SZ2atSjqW2r2TA.dat
```
Удалите индекс `test` и создайте индекс `test-2`. **Приведите в ответе** список индексов.

[Восстановите](https://www.elastic.co/guide/en/elasticsearch/reference/current/snapshots-restore-snapshot.html) состояние
кластера `elasticsearch` из `snapshot`, созданного ранее. 

**Приведите в ответе** запрос к API восстановления и итоговый список индексов.

Подсказки:
- возможно вам понадобится доработать `elasticsearch.yml` в части директивы `path.repo` и перезапустить `elasticsearch`

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

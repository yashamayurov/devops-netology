## Задача 1

Дайте письменые ответы на следующие вопросы:

- В чём отличие режимов работы сервисов в Docker Swarm кластере: replication и global?
```
Для replication указывается, сколько идентичных задач необходимо запустить.
Для global запускается одна задачу на каждой ноде
```
- Какой алгоритм выбора лидера используется в Docker Swarm кластере?
```
Используется так называемый алгоритм поддержания распределенного консенсуса — Raft.
```
- Что такое Overlay Network?
```
Логическая сеть Docker Swarm, работающая поверх основной сети узлов.
```

## Задача 2

Создать ваш первый Docker Swarm кластер в Яндекс.Облаке

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:
```
[root@node01 ~]# docker node ls
ID                            HOSTNAME             STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
ls58lshz74qz6i1jz951yz19x *   node01.netology.yc   Ready     Active         Leader           20.10.12
2f68uvwwif5np8twmzef089vz     node02.netology.yc   Ready     Active         Reachable        20.10.12
l63inponl9k4i33oewq1wt66q     node03.netology.yc   Ready     Active         Reachable        20.10.12
238runu2y4gd1zblu5khsjj6b     node04.netology.yc   Ready     Active                          20.10.12
jwg9dxjqgwvv7ojetao1g8k14     node05.netology.yc   Ready     Active                          20.10.12
sdwjzt074gfpg81xrhwt5zatw     node06.netology.yc   Ready     Active                          20.10.12
```

## Задача 3

Создать ваш первый, готовый к боевой эксплуатации кластер мониторинга, состоящий из стека микросервисов.

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:
```
[root@node01 ~]# docker service ls
ID             NAME                                MODE         REPLICAS   IMAGE                                          PORTS
xucxtk9b6qjw   swarm_monitoring_alertmanager       replicated   1/1        stefanprodan/swarmprom-alertmanager:v0.14.0
hmq61t1f466s   swarm_monitoring_caddy              replicated   1/1        stefanprodan/caddy:latest                      *:3000->3000/tcp, *:9090->9090/tcp, *:9093-9094->9093-9094/tcp
xnn400dozd2e   swarm_monitoring_cadvisor           global       6/6        google/cadvisor:latest
6hq4d6pb2sov   swarm_monitoring_dockerd-exporter   global       6/6        stefanprodan/caddy:latest
989decco448i   swarm_monitoring_grafana            replicated   1/1        stefanprodan/swarmprom-grafana:5.3.4
bk1h26uspwds   swarm_monitoring_node-exporter      global       6/6        stefanprodan/swarmprom-node-exporter:v0.16.0
z728c2vf0y4z   swarm_monitoring_prometheus         replicated   1/1        stefanprodan/swarmprom-prometheus:v2.5.0
i43agjqztflz   swarm_monitoring_unsee              replicated   1/1        cloudflare/unsee:v0.8.0
```

## Задача 4 (*)

Выполнить на лидере Docker Swarm кластера команду (указанную ниже) и дать письменное описание её функционала, что она делает и зачем она нужна:
```
# см.документацию: https://docs.docker.com/engine/swarm/swarm_manager_locking/
docker swarm update --autolock=true
```


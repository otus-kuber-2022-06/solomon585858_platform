# solomon585858_platform
solomon585858 Platform repository

## Homework 1
 - В рамках первого ДЗ был подготовлен образ docker на базе nginx для web сервера
 - Добавлены манифесты для web сервера на базе Docker образа
 - Добавлены манифесты для frontend компонента приложения Hipster Shop
 - Все файлы можно найти в папке kubernetes-intro

## Homework 2
 - Установлен локальный кластер kind
 - Подготовлен манифест Replicaset frontend-replicaset.yaml
 - Подготовлен Docker образ с тэгами v0.0.1/v0.0.2 для сервиса paymentservice
 - Подготовлен манифест Replicaset payment-replicaset.yaml
 - Подготовлен манифест Deployment payment-deployment.yaml
 - Выполнен отказ на предыдущую версию Deployment для сервиса paymentservice
 - С использованием параметров maxSurge и maxUnavailable реализованы дваr сценария - blue-green, Reverse Rolling Update
 - Подготовлен манифест Deployment frontend-deployment для сервиса frontend c Readiness Probe
 - Подготовлен манифест DaemonSet/Namespace для развертывания Node Exporter для Prometheus на всех узлах кластера Kubernetes

## Homework 3
 - Подготовлены манифесты для Service Accounts, Namespaces, Rolebindings, Clusterrolebindings для трех разных задач

## Homework 4
 - Проведена работа с тестовым веб-приложением
 - Настроен доступ к приложению извне кластера

## Homework 5
 - Подготовлены манифесы для StatefulSet, HeadlessService

## Homework 6
 - Развернут кластер GKE
 - Подготовлены HELM чарты для сервисов hipster-shop
 - Подготовлены манифесты с помощью kubecfg/kustomize

## Homework 7
 - Подготовлены CR, CRD, Operator для MySQL
```
$ kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           4s         2m21s
restore-mysql-instance-job   1/1           20s        89s
```
```
$ export MYSQLPOD=$(kubectl get pods -l app=mysql-instance -o jsonpath="{.items[*].metadata.name}")
$ kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+
```


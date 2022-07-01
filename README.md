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

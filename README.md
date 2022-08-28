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

## Homework 8
 - Подготовлены Deployment, Service, ServiceMonitor и СonfigMap для Nginx
 - Использован Nginx Exporter для передачи метрик в Prometheus
 - Развернут Prometheus на кластере Kubernetes с помощью Kubernetes Operator
```
Deploy Prometheus:
$ kubectl create namespace monitoring
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
$ helm repo update 
$ helm upgrade --namespace monitoring --install kube-stack-prometheus prometheus-community/kube-prometheus-stack --set prometheus-node-exporter.hostRootFsMount.enabled=false
```
```
Connect to Prometheus:
$ kubectl port-forward --namespace monitoring svc/kube-stack-prometheus-kube-prometheus 9090:9090
```
```
Connect to Grafana:
$ kubectl get secret --namespace monitoring kube-stack-prometheus-grafana -o jsonpath='{.data.admin-user}' | base64 -d
$ kubectl get secret --namespace monitoring kube-stack-prometheus-grafana -o jsonpath='{.data.admin-password}' | base64 -d
$ kubectl port-forward --namespace monitoring svc/kube-stack-prometheus-grafana 8080:80
```
[![Nginx-Prometheus-Exporter-Dashboard.png](https://i.postimg.cc/nrjrkXvK/Nginx-Prometheus-Exporter-Dashboard.png)](https://postimg.cc/K3bmcvp4)


## Homework 9
 - Проведено ознакомление с инструментами Flagger, Flux, Istio


```
$ kubectl describe pod -l app=frontend -n microservices-demo
Name:         frontend-7bf54b569-qkklb
Namespace:    microservices-demo
...
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  20s   default-scheduler  Successfully assigned microservices-demo/frontend-7bf54b569-qkklb to gke-mycluster-default-pool-be1086e0-2nml
  Normal  Pulling    17s   kubelet            Pulling image "solomon5858558/frontend:v0.0.2"
  Normal  Pulled     15s   kubelet            Successfully pulled image "solomon5858558/frontend:v0.0.2" in 2.630767651s
  Normal  Created    15s   kubelet            Created container server
  Normal  Started    15s   kubelet            Started container server
```
  
```$ helm history frontend -n microservices-demo
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION
1               Wed Aug 24 10:42:00 2022        superseded      frontend-0.21.0 1.16.0          Install complete
2               Wed Aug 24 10:56:51 2022        deployed        frontend-0.21.0 1.16.0          Upgrade complete
```
```
Events:
  Type     Reason  Age                From     Message
  ----     ------  ----               ----     -------
...
  Normal   Synced  5m30s              flagger  New revision detected! Scaling up frontend.microservices-demo
  Normal   Synced  4m30s              flagger  Starting canary analysis for frontend.microservices-demo
  Normal   Synced  4m30s              flagger  Advance frontend.microservices-demo canary weight 5
  Normal   Synced  3m30s              flagger  Advance frontend.microservices-demo canary weight 10
  Normal   Synced  2m20s               flagger  Advance frontend.microservices-demo canary weight 15
  ...
```
